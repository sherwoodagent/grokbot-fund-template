---
name: propose
description: Use to turn Sherwood /sources data plus your own analysis into a Portfolio or MorphoSupply proposal, self-critiqued and shown to the owner before signing.
---
# Propose

Flags and builders: Sherwood skill → `Strategy Templates` (`portfolio`, `morpho-supply`); bond and coverage: `Tiers, coverage, and the proposer bond`. Run `sherwood strategy list` first. If a template isn't deployed on the chain, don't draft it.

## 1. Pull data (logged in via `sherwood sources login`; on 401 re-login first)
```
sherwood sources get calendar                       # session + weekend StalePrice window
sherwood sources get prices                         # price_usd, age_s, portfolio_price_ok
sherwood sources get liquidity | jq '.recommended.by_symbol'
sherwood sources get morpho --amount <N> | jq '.recommended'
```
Every record has `last_updated`. Treat stale data as unknown.

## 2. Draft one of
**A. Portfolio basket.** 3–5 stock tokens. For each leg use the pool from `liquidity.recommended.by_symbol` (the skill shows how it maps to `--swap-routes` / `--fee-tier`). Drop any leg with `portfolio_price_ok=false` or with no recommended pool. Weights in bps sum to 10000. `--max-slippage` is one value for the whole basket: set it to the **largest** `suggested_max_slippage_bps` among the legs, and note the per-leg values in `draft.md`. Optionally pass `--max-slippage-per-leg <a,b,c>` (same order as `--tokens`) instead; the strategy stores one value, so it resolves to the largest leg either way.
**B. MorphoSupply** `UNAUDITED`: `--market-id recommended --amount <N>`. It targets one Blue market, not a vault. Never use a market under `recommended.rejected`. Check withdrawable liquidity ≥ amount at settle.
Size inside free guardian coverage, and keep the proposer bond liquid.

## 3. Your analysis (required)
In `workspace/proposals/<id>/draft.md`: thesis in your own words, why this beats the `recommended` pick or why you kept it, and the data `last_updated` timestamps you relied on.

## 4. Self-critique (checker pass)
- Bear case per leg or market, and "What could I be wrong about?"
- Kill criteria, and what triggers an early settle. Set `--max-drawdown-bps` to match them: **2000 (20%) by default**, tighter if your kill criteria say so. Always pass it explicitly.
- Risks: weekend window, slippage at size, utilization or withdrawability, unaudited label, coverage and bond
- Verdict: `GO` / `REWORK` / `PASS`. On REWORK, go back to step 2 once. On PASS, tell the owner and offer 2–3 alternatives.

## 5. Owner yes
The wallet is sign-only, so the proposal is built keyless with the root `--calldata-only` flag:
```
sherwood --calldata-only strategy propose <portfolio|morpho-supply> --vault <vault> \
  --proposer <agent wallet> --metadata-uri ipfs://… \
  --max-slippage <largest leg bps> --max-drawdown-bps <bps, default 2000> --duration 30d \
  <strategy flags from the skill>
```
No `--metadata-uri`? Pass `--name` and `--description` and the CLI pins them for you. The proposer bond approval is the first tx in `txs`.
Show the draft summary, the exact command and the `txs` it printed. Sign and broadcast the txs in order only after a yes. Then run `sherwood proposal show --id <id> --vault <vault>`, save its strategy provenance (template, approved or not) as `provenance.json`, flag anything not an approved template to the owner, and enable `proposal-watch`.
