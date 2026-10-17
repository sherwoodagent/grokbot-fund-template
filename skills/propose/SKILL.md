---
name: propose
description: Use to turn Sherwood /sources data (optional; Chainlink fallback) plus your own analysis into a Portfolio or MorphoSupply proposal, self-critiqued and shown to the owner before signing.
---
# Propose

Flags and builders: Sherwood skill → `Strategy Templates` (`portfolio`, `morpho-supply`); bond and coverage: `Tiers, coverage, and the proposer bond`. Run `sherwood strategy list` first. If a template isn't deployed on the chain, don't draft it.

## 0. Preflight (every proposal)
- **Fresh yes after a kill:** if the last proposal was killed (vetoed, cancelled, rejected, expired, or aborted), don't draft until the owner gives a fresh yes in this chat to propose again.
- **Feed freshness:** check the protocol price feeds for every asset the draft touches (`sherwood sources get prices` → `age_s`, `portfolio_price_ok`; `sources get calendar`; without `/sources`, the fallback in step 1; and any staleness warning the CLI or guardian prints). If any feed is stale, stop: don't propose, and tell the owner which feed is stale and since when. Routines do the same and message the owner.
- **Official stock tokens (SHE-405):** check every Portfolio leg's token address against Robinhood's official stock token list before drafting. Symbols and names prove nothing: a fake NVDA was airdropped to ~20K wallets.
  ```
  curl -sf https://api.robinhood.com/rhj/assets | jq -r '.assets[] | select(.status=="ASSET_STATUS_ACTIVE") | .tokenSymbol as $s | .deployments[] | select(.chainId==4663) | "\($s) \(.contractAddress | ascii_downcase)"'
  ```
  A leg passes only if its address (lowercased) is on the list under the same symbol. Refuse any leg that is unknown, or whose symbol matches but address differs; tell the owner the address and why. If the list can't be fetched or comes back empty, don't propose this round and tell the owner. Pass the checked addresses to `--tokens`, not symbols.

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
```
A stale USDG feed blocks every propose. Skip the liquidity and Morpho recommendations (step 2), and tell the owner this draft ran without `/sources`.

## 2. Draft one of
**A. Portfolio basket.** 3–5 stock tokens. For each leg use the pool from `liquidity.recommended.by_symbol` (the skill shows how it maps to `--swap-routes` / `--fee-tier`). Drop any leg with `portfolio_price_ok=false` or with no recommended pool. Without `/sources`: drop any leg whose feed is older than 26h, leave its `--swap-routes` slot empty (the CLI quotes single-hop fee tiers itself, a fallback), set `--max-slippage` yourself and say so in `draft.md`. Weights in bps sum to 10000. `--max-slippage` is one value for the whole basket: set it to the **largest** `suggested_max_slippage_bps` among the legs, and note the per-leg values in `draft.md`. Optionally pass `--max-slippage-per-leg <a,b,c>` (same order as `--tokens`) instead; the strategy stores one value, so it resolves to the largest leg either way.
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
No `--metadata-uri`? Pass `--name` and `--description` and the CLI pins them for you. The proposer bond approval is the first tx in `txs`.

**Attest (when `fund.json` `attest` is true, the default).** Add these flags to the same command:
```
  --attest-thesis workspace/proposals/<id>/thesis.md --direction <long|short|neutral> \
  --confidence <bps> --license <fund.json attestLicense>
```
`--horizon` defaults to `--duration` and `--thesis-asset <symbol|address>` defaults to the vault asset; set them only to override. The thesis is pinned in plaintext and public. Add `--encrypt --key-file <path>` only if the owner asks; the owner keeps the key, nobody else holds a copy. The thesis attestation tx comes with the propose txs and rides on the same yes. If it fails, log it in `journal.md` and carry on: an attestation never blocks a proposal. Never pass `--skip-schema-check`: it builds a preview only, and a broadcast before the schema is registered reverts.

Show the draft summary, the exact command and the `txs` it printed. Sign and broadcast the txs in order only after a yes. Then run `sherwood proposal show <id> --vault <vault>`, save its strategy provenance (template, approved or not) as `provenance.json`, flag anything not an approved template to the owner, and enable `proposal-watch`.

**Link (keyless; the proposal id is known only after the propose confirms).** Read the thesis UID from the broadcast thesis attest tx, then link:
```
sherwood attest uid --tx <thesis attest tx hash>     # → { uid, schema, attester, recipient }
sherwood --calldata-only attest link --vault <vault> --proposal-id <id> --thesis-uid <uid> --license <attestLicense>
```
`--provenance-class` defaults to `auto`.
→ sign/broadcast (covered by the same yes). Save `{ "thesisUid", "linkUid", "license" }` in `workspace/proposals/<id>/attestations.json`. On failure, log it and continue.
