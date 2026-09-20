# Risk note
lens:munger

## Decision
**APPROVE** (paper-only / no-trade)

## Reason
Empty basket + `status: no-trade` is the correct paper pass — Critic CLEARED quality but thesis still bearish; `rhBasis` UNKNOWN blocks live.

## Live
**FORBIDDEN.** `rhBasis: UNKNOWN`, `liveReady: false`, weights `[]`. Vault USDG balance is not an Ops unlock. No live PortfolioStrategy until Scanner marks basis known (or owner-accepted haircut on record).

## Cap breaches
None (weights empty — ≤5 / ≤30% unused).

## Checklist (hard vetoes)
| Gate | Result |
|------|--------|
| Opaque / custom calldata | Pass — PortfolioStrategy only |
| Single name > 30% | N/A — empty basket |
| > 5 names | N/A — empty basket |
| Kill criteria from Critic | Pass — NFLX / AMZN / RH / process kills present in draft + critic |
| Privy → raw Ops discipline | N/A — not an Ops request |
| Shared-file artifacts | Pass — draft / critic / scan / mandate present |

## Invert
How we die: sizing RH tokens as cash with unmeasured basis, or inventing longs into Wells −20.6% asymmetry. This draft does neither. Re-gate when mids print or PM sizes.

## Handoff
Desk Lead: paper gate formalized. Ops: do not touch chain. Scanner: unblock only after fork mid vs cash.
