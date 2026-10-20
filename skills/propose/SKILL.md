---
name: propose
description: Use to turn Sherwood /sources data (optional; Chainlink fallback) plus your own analysis into a Portfolio or MorphoSupply proposal, self-critiqued and shown to the owner before signing.
---
# Propose

Flags and builders: Sherwood skill → `Strategy Templates` (`portfolio`, `morpho-supply`); bond and coverage: `Tiers, coverage, and the proposer bond`. Run `sherwood strategy list` first. If a template isn't deployed on the chain, don't draft it.

**On the MCP path** (`fund-setup` → `2b`), use the matching tools instead of `sherwood` commands. Check with `check_propose`, quote with `quote_proposer_bond`, and build with `prepare_propose` (its bond approve is already quote + 5%, bounded, and skipped when the allowance covers it). Pin with `pin_metadata` and attest with `prepare_attest`. There is no `/sources` on the MCP, so always use the no-`/sources` fallback in step 1. `get_feed_status` covers the WOOD/USD and vault-asset feeds. Read stock feeds with `curl`, as in step 1. `prepare_strategy_deploy` needs template `initData` you encode yourself, and you also supply `executeCalls` / `settleCalls`. If you can't build and simulate those with confidence (`simulate_batch`; a full governor batch can revert `0x8485a0a6`, a known server issue), use the CLI for this proposal. Every rule in this skill still applies.

## 0. Preflight (every proposal)
- **Fresh yes after a kill:** if the last proposal was killed (vetoed, cancelled, rejected, expired, or aborted), don't draft until the owner gives a fresh yes in this chat to propose again.
- **Governor cooldown:** after any cancel or settle, the vault's governor blocks a new propose for **1 hour on 4663** (`cooldownPeriodSeconds` = 3600); proposing earlier reverts `CooldownNotElapsed`. Read the end with `sherwood governor info --vault <vault>` (MCP: `get_governor` → `cooldownEnd`, or `check_propose`) and wait it out in chain time before you send. Also wait while another proposal is still open (`VaultHasOpenProposal`).
- **Feed freshness:** check the protocol price feeds for every asset the draft touches (`sherwood sources get prices` → `age_s`, `portfolio_price_ok`; `sources get calendar`; without `/sources`, the fallback in step 1; and any staleness warning the CLI or guardian prints). If any feed is stale, stop: don't propose, and tell the owner which feed is stale and since when. Routines do the same and message the owner.
- **Stock-ticker check (SHE-405):** only a token that claims an official Robinhood stock ticker has to match that ticker's official address. Symbols and names prove nothing on their own: at least 13 tokens on 4663 use the symbol NVDA, and a fake NVDA was airdropped to ~20K wallets.
  1. Get the list (no install beyond `curl` + `jq`):
     ```
     curl -sf --max-time 20 https://api.robinhood.com/rhj/assets | jq '{fetched_at: (now|todate), source: "https://api.robinhood.com/rhj/assets", tokens: [.assets[] | select(.status=="ASSET_STATUS_ACTIVE") | {symbol: .tokenSymbol, name: .tokenName} + (.deployments[] | select(.chainId==4663) | {address: (.contractAddress|ascii_downcase)})]}' > /tmp/official.json && [ "$(jq '.tokens|length' /tmp/official.json)" -gt 0 ] && mv /tmp/official.json workspace/official-stock-tokens.json
     ```
     On success this refreshes the local cache `workspace/official-stock-tokens.json` (gitignored; never commit it). If the fetch fails or comes back empty, use that cache and note its `fetched_at` in `draft.md`. If there is no cache either, don't block: say so in `draft.md` and to the owner, and rely on the CLI and onchain checks (Portfolio only prices tokens paired with a real feed in TierRegistry, so a fake can't be priced by a real feed).
  2. For each token the draft touches, read its on-chain `symbol()` and `name()` (and the ticker you or the data call it by). Normalize: trim, uppercase, drop zero-width characters. It **claims a ticker** if that equals an official `symbol`, or its name copies an official `name`.
  3. **Claims a ticker → the address (lowercased) must equal that ticker's official address.** If it differs, refuse the token: it is an impersonator. Tell the owner the address, the ticker it claims and the official address.
  4. **Claims no official ticker → not blocked by this check.** Run the normal hygiene checks: the address has contract code, `symbol()` / `decimals()` read, and a quote exists on a listed venue. Record thin liquidity, a missing oracle, contract age or admin powers as risks in `draft.md`; none of them blocks on its own. The CLI, the guardian and the onchain pairings still decide what a strategy accepts: if they refuse a token, follow them.
  Pass the checked addresses to `--tokens`, not symbols.

## 1. Pull data (`/sources` if logged in via `sherwood sources login`; on 401 re-login first)
```
sherwood sources get calendar                       # session + weekend StalePrice window
sherwood sources get prices                         # price_usd, age_s, portfolio_price_ok
sherwood sources get liquidity | jq '.recommended.by_symbol'
sherwood sources get morpho --amount <N> | jq '.recommended'
```
Every record has `last_updated`. Treat stale data as unknown.

**No `/sources` (403, no login or no access; it is cohort-only and experimental):** read each feed's age directly. Feed addresses: skill → `ADDRESSES.md` / docs [Deployments](https://docs.sherwood.sh/reference/deployments#stock-tokens-and-chainlink-feeds) (`CHAINLINK_<SYM>_USD_FEED`, plus USDG/USD).
```
cast call <feed> "latestRoundData()(uint80,int256,uint256,uint256,uint80)" --rpc-url "${ROBINHOOD_RPC_URL:-https://rpc.mainnet.chain.robinhood.com}"
# 4th value = updatedAt; age = chain time − updatedAt. Stale if > 26h (93600 s); the StalePrice window ends at the feed's next update
# without Foundry: eth_call latestRoundData() (selector 0xfeaf968c); updatedAt is the 4th 32-byte word
curl -s "${ROBINHOOD_RPC_URL:-https://rpc.mainnet.chain.robinhood.com}" -H 'content-type: application/json' -d '{"jsonrpc":"2.0","id":1,"method":"eth_call","params":[{"to":"<feed>","data":"0xfeaf968c"},"latest"]}' | jq -r '.result[194:258]'   # hex updatedAt
```
A stale USDG feed blocks every propose. Skip the liquidity and Morpho recommendations (step 2), and tell the owner this draft ran without `/sources`.

## 2. Draft one of
**A. Portfolio basket.** 3–5 tokens: official stock tokens, or any other token (memecoins included) that passes the step 0 hygiene and impersonator checks. For each leg use the pool from `liquidity.recommended.by_symbol` (the skill shows how it maps to `--swap-routes` / `--fee-tier`). Drop a leg only if its feed is stale (`portfolio_price_ok=false` on a leg that has a feed; a stale feed makes Portfolio revert). A leg with no feed or no recommended pool is not dropped by the desk for that alone. Flag it in `draft.md`, and if the CLI or the onchain pairing (TierRegistry) refuses it, follow them. For a leg with no recommended pool, leave its `--swap-routes` slot empty (the CLI quotes single-hop fee tiers itself) and flag the thin liquidity in `draft.md`. Without `/sources`: drop any leg whose feed is older than 26h (a leg with no feed: as above), leave its `--swap-routes` slot empty (the CLI quotes single-hop fee tiers itself, a fallback), set `--max-slippage` yourself and say so in `draft.md`. Weights in bps sum to 10000. `--max-slippage` is one value for the whole basket: set it to the **largest** `suggested_max_slippage_bps` among the legs, and note the per-leg values in `draft.md`. Optionally pass `--max-slippage-per-leg <a,b,c>` (same order as `--tokens`) instead; the strategy stores one value, so it resolves to the largest leg either way.
**B. MorphoSupply** `UNAUDITED`: `--market-id recommended --amount <N>`. It targets one Blue market, not a vault. Never use a market under `recommended.rejected`. Check withdrawable liquidity ≥ amount at settle. Without `/sources` there is no `recommended` market: skip MorphoSupply this round and tell the owner.
Size inside free guardian coverage and the protocol and vault caps, and keep the proposer bond liquid. Execute needs that coverage booked during the guardian review: the `watch` skill checks it with `proposal show` before execute.

## 3. Your analysis (required)
In `workspace/proposals/<id>/draft.md`: thesis in your own words (also saved alone as `thesis.md` for the attestation; public by default, so no private notes in it), why this beats the `recommended` pick or why you kept it, and the data `last_updated` timestamps you relied on.

## 4. Self-critique (checker pass)
- Bear case per leg or market, and "What could I be wrong about?"
- Kill criteria, and what triggers an early settle. Set `--max-drawdown-bps` from them (and the skill’s guidance), and always pass it explicitly.
- Risks: weekend window, slippage at size, utilization or withdrawability, unaudited label, coverage and bond
- Verdict: `GO` / `REWORK` / `PASS`. On REWORK, go back to step 2 once. On PASS, tell the owner and offer 2–3 alternatives.

## 4b. Management plan (required)
Follow the Sherwood skill's management-plan step (`references/management-plan.md`). Research this strategy type's own mechanics from its contract and docs (what can be adjusted mid-proposal, how risk limits and settle work, what happens at expiry) and the event risk in the window. Write `workspace/proposals/<id>/plan.md` with the triggers, risk levels (set inside any limit that can block settle, math shown), target settle window, check schedule (default: twice on weekdays, owner local time) and reminder times. `plan.md` stays private: never pin, attest or post it. Add to `thesis.md` (public) and `--description` only a short summary with no exact risk levels, trigger prices or settle times, e.g. "Management plan agreed: periodic rebalance checks, early-settle rules, target settle before <event>." Show the owner the plan with the draft; the owner's yes in step 5 covers the plan too. The plan never lets you transact on your own.

## 5. Owner yes
The wallet is sign-only, so the proposal is built keyless with the root `--calldata-only` flag:
```
sherwood --calldata-only strategy propose <portfolio|morpho-supply> --vault <vault> \
  --proposer <agent wallet> --metadata-uri ipfs://… \
  --max-slippage <largest leg bps> --max-drawdown-bps <bps from your kill criteria> --duration 30d \
  <strategy flags from the skill>
```
No `--metadata-uri`? Pass `--name` and `--description` and the CLI pins them for you.

**Proposer bond: approve the quote + 5%, capped.** The WOOD bond quote moves with the WOOD price between quote and propose (we've seen +0.22 WOOD within minutes), so an exact approve makes `propose` revert. The bond approval is the first tx in `txs`, and the latest CLI already builds it as quote + 5% (it prints `Buffer (5%) … approve up to <n> WOOD (bounded)`). Before signing, decode that approve (`approve(address,uint256)`: spender = the bond escrow, amount = last 32 bytes) and check:

- amount = quoted bond + 5% (`quote + quote × 500 / 10000`, as the CLI computes it). Never more, and never unlimited (`0xff…ff` / `maxUint256`): refuse an unlimited approve.
- If it is exact or missing the buffer, don't sign it. Send `approve(<escrow>, quote + quote × 500 / 10000)` on the WOOD token instead, and show it to the owner with the rest.
- No approve tx: the existing allowance already covers it. Check it is ≥ the quote.
- The wallet holds at least the quote (keep quote × 1.05 liquid). Send propose right after the approve. If it still reverts on the bond (drift > 5%), re-run the propose command to re-quote; never widen the buffer or approve unlimited.

**Clone tx: skip it when the clone already exists.** For a strategy with a clone, `txs` include `cloneAndInitDeterministic` on the StrategyFactory, and the output's `clone` field is the predicted address (pinned by factory, template, vault and salt). After a cancel and repropose with the same inputs, the clone is already deployed, yet the CLI still emits that tx and it reverts. Before broadcasting, check the clone's code:

```bash
cast code <clone> --rpc-url "${ROBINHOOD_RPC_URL:-https://rpc.mainnet.chain.robinhood.com}"
# or, without Foundry:
curl -s "${ROBINHOOD_RPC_URL:-https://rpc.mainnet.chain.robinhood.com}" -H 'content-type: application/json' -d '{"jsonrpc":"2.0","id":1,"method":"eth_getCode","params":["<clone>","latest"]}' | jq -r .result
```

If it returns code (anything but `0x`), skip the clone tx and send the remaining txs in order (bond approve, then propose). If it returns `0x`, send every tx. Never skip any other tx, and never reorder.

**Attest (when `fund.json` `attest` is true, the default).** Add these flags to the same command:
```
  --attest-thesis workspace/proposals/<id>/thesis.md --direction <long|short|neutral> \
  --confidence <bps> --license <fund.json attestLicense>
```
`--horizon` defaults to `--duration` and `--thesis-asset <symbol|address>` defaults to the vault asset; set them only to override. The thesis is pinned in plaintext and public. Add `--encrypt --key-file <path>` only if the owner asks; the owner keeps the key, nobody else holds a copy. The thesis attestation tx comes with the propose txs and rides on the same yes. If it fails, log it in `journal.md` and carry on: an attestation never blocks a proposal. Never pass `--skip-schema-check`: it builds a preview only, and a broadcast before the schema is registered reverts.

Show the draft summary, the exact command and the `txs` it printed. Sign and broadcast the txs in order (minus a clone tx skipped above) only after a yes. Then run `sherwood proposal show <id> --vault <vault>`, save its strategy provenance (template, approved or not) as `provenance.json`, flag anything not an approved template to the owner, and enable `proposal-watch`.

**Link (keyless; the proposal id is known only after the propose confirms).** Read the thesis UID from the broadcast thesis attest tx, then link:
```
sherwood attest uid --tx <thesis attest tx hash>     # → { uid, schema, attester, recipient }
sherwood --calldata-only attest link --vault <vault> --proposal-id <id> --thesis-uid <uid> --license <attestLicense>
```
`--provenance-class` defaults to `auto`.
→ sign/broadcast (covered by the same yes). Save `{ "thesisUid", "linkUid", "license" }` in `workspace/proposals/<id>/attestations.json`. On failure, log it and continue.
