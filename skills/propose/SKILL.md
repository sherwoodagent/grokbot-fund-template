---
name: propose
description: Use to turn Sherwood /sources data plus your own analysis into a Portfolio or MorphoSupply proposal, self-critiqued and shown to the owner before signing.
---
# Propose

Flags and builders: Sherwood skill → Strategies (`portfolio`, `morpho-supply`). Run `sherwood strategy list` first. If a template isn't deployed on the chain, don't draft it.

## 1. Pull data (logged in via `sherwood sources login`)
```
sherwood sources get calendar                       # session + weekend StalePrice window
sherwood sources get prices                         # price_usd, age_s, portfolio_price_ok
sherwood sources get liquidity | jq '.recommended.by_symbol'
sherwood sources get morpho --amount <N> | jq '.recommended'
```
Every record has `last_updated`. Treat stale data as unknown.

## 2. Draft one of
**A. Portfolio basket.** 3–5 stock tokens. For each leg use the pool and `maxSlippageBps` from `liquidity.recommended.by_symbol`. Drop any leg with `portfolio_price_ok=false` or with no recommended pool. Weights in bps sum to 10000.
**B. MorphoSupply** `UNAUDITED`: `strategy propose morpho-supply --market-id recommended --amount <N>`. It targets one Blue market, not a vault. Never use a market under `recommended.rejected`. Check withdrawable liquidity ≥ amount at settle.
Default `--duration 30d`. Size inside free guardian coverage, and keep the proposer bond liquid.

## 3. Your analysis (required)
In `workspace/proposals/<id>/draft.md`: thesis in your own words, why this beats the `recommended` pick or why you kept it, and the data `last_updated` timestamps you relied on.

## 4. Self-critique (checker pass)
- Bear case per leg or market, and "What could I be wrong about?"
- Kill criteria, and what triggers an early settle
- Risks: weekend window, slippage at size, utilization or withdrawability, unaudited label, coverage and bond
- Verdict: `GO` / `REWORK` / `PASS`. On REWORK, go back to step 2 once. On PASS, tell the owner and offer 2–3 alternatives.

## 5. Owner yes
Show the draft summary, the exact CLI command, the simulation and the provenance output (save it as `provenance.json`). Submit only after a yes. Then enable `proposal-watch`.
