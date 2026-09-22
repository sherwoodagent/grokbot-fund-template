# Fund mandate (beta) — growth edition

- **Venue:** Sherwood on RH fork (chain `9994663`) — test capital only
- **Goal:** Grow the book creatively inside protocol rails. Paper new sleeves before live. One live proposal at a time.

## Strategy classes (allowed)

| Priority | Class | Use |
|----------|-------|-----|
| 1 | **PortfolioStrategy** | Primary — tokenized equities/ETFs with feeds + RH basis |
| 2 | **MorphoSupplyStrategy** | Yield / float sleeve between equity books (fork template) |
| 3 | **ConcentratedLiquidityStrategy** | LP sleeve on **listed** fork pools only |
| 4 | Future templates | Only if `sherwood strategy list` shows them + TierRegistry counterparties bind |

**Forbidden:** opaque free-form calldata, memecoins, unlisted venues, strategies with no settle/unwind path, inventing assets not on the fork universe.

## Book structure

- **Core (60–100% of capital intent):** quality/value RH equities via PortfolioStrategy  
  - Max **5** names · max single **30%** · default duration **7d** (≤ governor max)
- **Satellite (0–40%):** growth / asymmetric / yield — Morpho, CL, or **equity proxies** of crypto theses when the economic link is real and the name is fork-tradable with basis OK
- Protocol still allows **one live proposal at a time** — satellites are sequential books (settle → next propose), not parallel vaults

## Research tracks (dual)

| Track | Output | Live? |
|-------|--------|-------|
| **A — Equities** | PortfolioStrategy basket (stocks/ETFs on fork) | Yes, after Scanner basis + Critic + Risk + owner GO |
| **B — Crypto asymmetric** | 5–10 scored finalists via `crypto-asymmetric-research` skill | **Paper by default.** Live only if mapped to Track A proxies or a listed crypto-capable template |

Bridge rule: never force a stock proxy for a crypto thesis. Map only when the mechanism is honest (e.g. AI infra demand → NVDA/MSFT), then Critic must CLEARED the map.

## Live gates (unchanged discipline)

1. Scanner: `rhBasis` known; `liveReady` true for the proposed set (or owner-accepted haircut)  
2. Research → Critic (**Critic waits**) → PM → Risk  
3. Risk `APPROVE` · owner `GO` · Ops Privy sign → raw broadcast  
4. Watch through **Settled**, not just Executed  
5. Proposer may early-settle (≥ ~1h after execute); permissionless after full duration  

## Personas

- research = `lynch_buffett` (+ Track B skill when owner asks crypto screen)
- critic = `burry` (must include kill criteria + “WHAT COULD I BE WRONG ABOUT?”)
- pm = `druckenmiller`
- risk = `munger`

## Growth posture

Bias to **run experiments that produce signal** (cheap duration, clear kill criteria, basis-gated). Prefer a Morpho float or a tight thematic equity book over sitting in cash after settle — but never skip Critic/Risk to “be creative.”
