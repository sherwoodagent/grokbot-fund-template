# Advanced / growth (opt-in)

**Not the default.** New Grok Bot users start on PortfolioStrategy + paper-first (`workspace/strategies.md`). Use this doc only when the owner explicitly opts into satellites or crypto-asymmetric research.

## When to opt in

- Owner asks for Morpho float, CL LP, or a crypto screen
- Ops has documented market/pool ids (see `workspace/ops/morpho-cl-cookbook.md`)
- Critic can CLEAR without inventing fork liquidity

## Strategy classes beyond PortfolioStrategy

| Priority | Class | Use |
|----------|-------|-----|
| 2 | **MorphoSupplyStrategy** | Yield / float sleeve between equity books (fork template) |
| 3 | **ConcentratedLiquidityStrategy** | LP sleeve on **listed** fork pools only |
| 4 | Future templates | Only if `sherwood strategy list` shows them + TierRegistry counterparties bind |

### Book structure (when growth is on)

- **Core (60–100% of capital intent):** quality RH equities via PortfolioStrategy
- **Satellite (0–40%):** Morpho, CL, or **equity proxies** of crypto theses when the economic link is real and the name is fork-tradable with basis OK
- Still **one live proposal at a time**

### Morpho / CL honesty (do not soft-pedal)

Canonical Ops write-up: `workspace/ops/morpho-cl-cookbook.md`. Summary:

| Sleeve | On fork? | Concrete IDs? | CLI propose? | Ops posture |
|--------|----------|---------------|--------------|-------------|
| `PortfolioStrategy` | yes | n/a | **yes** | Live path (starter default) |
| **MorphoSupply** | yes — Morpho Blue + USDG loan markets | yes — cookbook / skill example | **yes** `morpho-supply` (CLI runs init checks) | Opt-in; Critic/Risk pick the market; owner GO |
| **ConcentratedLiquidity** | yes — V3 WETH/USDG + Morpho borrow | skill example (USDG/WETH, spUSDG market) | **yes** `concentrated-liquidity` | Opt-in; **not** v4 equity LP (template is V3 + Morpho leverage); counterparty preflight must pass |

- Morpho without cookbook market id → Critic **BLOCKED**
- CL framed as v4 equity LP or without V3 pool + Morpho collateral path → **BLOCKED**
- Both are tier 2: full-notional guardian coverage + ≈1% WOOD proposer bond. Size to free coverage (`skills/sherwood-ops`)

## Dual research tracks (opt-in)

| Track | Output | Live? |
|-------|--------|-------|
| **A — Equities** | PortfolioStrategy basket (stocks/ETFs on fork) | Yes, after Scanner basis + Critic + Risk + owner GO |
| **B — Crypto asymmetric** | 5–10 scored finalists via `crypto-asymmetric-research` skill | **Paper by default.** Live only if mapped to Track A proxies or a listed crypto-capable template |

**Bridge rule:** never force a stock proxy for a crypto thesis. Map only when the mechanism is honest (e.g. AI infra demand → NVDA/MSFT), then Critic must CLEARED the map.

### Wiring

- Research: skill `crypto-asymmetric-research` → `briefs/crypto-asymmetric.md` + FORK TRANSLATION
- Critic: bear + **WHAT COULD I BE WRONG ABOUT?** + attack proxy maps
- Desk Lead: kick Track B only when owner asks; do not make dual-track the morning default

## Growth posture

Bias to **run experiments that produce signal** (cheap duration, clear kill criteria, basis-gated). Prefer a Morpho float or a tight thematic equity book over sitting in cash after settle — but never skip Critic/Risk to “be creative.”
