# Risk note — Owner basket 3 (AI Delivery)
lens:munger

## Decision
**APPROVE** (paper-only)

## Reason
Caps clear (≤5 / ≤3000 bps) and Critic CLEARED paper — live still forbidden on `rhBasis` UNKNOWN + Research gap on 4/5 names.

## Live
**FORBIDDEN.** `rhBasis: UNKNOWN`, `liveReady: false`, status `paper`. Vault USDG is not an Ops unlock. No live submit until Scanner marks basis known (or owner-accepted haircut on record).

## Cap check
| Sym | bps | Cap ≤3000 | ≤5 names |
|-----|-----|-----------|----------|
| MSFT | 2800 | Pass | |
| GOOGL | 2500 | Pass | |
| NVDA | 2000 | Pass | |
| AMZN | 1500 | Pass | |
| QQQ | 1200 | Pass | |
| **Sum** | **10000** | | **5 / 5 Pass** |

**Cap breaches:** none.

## Checklist (hard vetoes)
| Gate | Result |
|------|--------|
| Opaque / custom calldata | Pass — PortfolioStrategy |
| Single name > 3000 bps (30%) | Pass — max MSFT 2800 |
| > 5 names | Pass — exactly 5 |
| Kill criteria from Critic | Pass — per-name + factor/RH/process kills in draft + critic |
| Privy → raw Ops discipline | N/A — not an Ops request |
| Shared-file artifacts | Pass — draft / critic / scan / mandate |
| Live without measured RH basis | Fail → live blocked (not paper veto) |

## Invert / non-optional flags (not redesign)
How we die: Mon AI-factor gap hits all five; nested QQQ reloads NVDA beta; MSFT soft-Fri “value entry” is folklore; Research still NFLX-era — no tables for MSFT/GOOGL/NVDA/QQQ.

Logged for next loop (REVISE→PM if they want deeper conviction; Risk does not cut weights):
1. Research gap on 4/5 names
2. Nested QQQ beta inside AI cluster
3. Value-mandate vs AI-factor book tension
4. `rhBasis` UNKNOWN

## Handoff
Desk Lead: paper gate formalized for basket 3. Ops: idle. Scanner: fork mids before any live ask. Research: new pass on MSFT/GOOGL/NVDA/QQQ if conviction rises.
