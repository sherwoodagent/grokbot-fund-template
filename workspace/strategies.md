# Sherwood strategies (beta)

What you can do with this desk on the RH fork (`9994663`) — test capital only.

## Default / starter path (what new users see first)

| Piece | Choice |
|-------|--------|
| **Strategy** | **PortfolioStrategy** only |
| **Mode** | Paper first — full Scanner → Research → Critic → PM → Risk loop before any chain write |
| **Live** | One PortfolioStrategy book at a time; settle (or reject/cancel) before the next propose |
| **Book** | Max **5** names · max single **30%** · default duration **7d** (≤ governor max) |
| **Universe** | Fork-listed tokenized equities/ETFs with feeds + RH basis OK |

**Forbidden on every path:** opaque free-form calldata, memecoins, unlisted venues, strategies with no settle/unwind path, inventing assets not on the fork universe.

## Optional satellites (opt-in — not the default personality)

MorphoSupply and ConcentratedLiquidity are available as **growth/advanced** sleeves. They are **not** what the starter template sells by default. See `docs/advanced-growth.md` for:

- When to opt in
- Morpho / CL honesty (IDs, CLI gaps, Critic gates)
- Dual Track A/B crypto-asymmetric research (paper by default)

Protocol still allows **one live proposal at a time** — satellites are sequential books (settle → next propose), not parallel vaults.

## Live discipline (all paths)

1. Scanner: `rhBasis` known; `liveReady` true for the proposed set (or owner-accepted haircut)
2. Research → Critic (**Critic waits**) → PM → Risk
3. Risk `APPROVE` · owner `GO` · Ops Privy sign → raw broadcast
4. Watch through **Settled**, not just Executed
5. Proposer may early-settle (≥ ~1h after execute); permissionless after full duration

## Personas (lenses, not LARPing)

- research = `lynch_buffett`
- critic = `burry` (kill criteria + “WHAT COULD I BE WRONG ABOUT?”)
- pm = `druckenmiller`
- risk = `munger`

## Posture

Bias to **signal** (cheap duration, clear kill criteria, basis-gated). Prefer a clean PortfolioStrategy paper loop over skipping Critic/Risk. Advanced sleeves only when the owner opts in and Ops IDs are documented.
