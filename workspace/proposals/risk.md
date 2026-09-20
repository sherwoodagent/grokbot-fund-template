# Risk note — Owner basket 3 LIVE RE-GATE
lens:munger

## Decision
**REJECT** (live-candidate)

## Reason
Owner haircut clears RH basis only — Critic live-depth kill still open (Research missing on MSFT/GOOGL/NVDA/QQQ); no live APPROVE on thin thesis.

## What cleared
| Item | Status |
|------|--------|
| Caps ≤5 / ≤3000 bps | Pass — MSFT 2800 max, sum 10000 |
| Kill criteria present | Pass — draft carries haircut-aware kills |
| `rh-basis.md` (fedcf68) | Pass — cash↔oracle tight; NVDA ~199 bps / AMZN ~264 bps pool vs feed |
| Owner haircut on record | Pass — `haircutAccepted` in draft (NVDA 199 / AMZN 264 bps) |
| Opaque calldata / Privy skip | N/A — not an Ops propose |

## What blocks live
| Item | Status |
|------|--------|
| Research depth (MSFT/GOOGL/NVDA/QQQ) | **Fail** — Critic CLEARED *paper* only; live-depth kill: size live without Research pass → REJECT depth. Draft still admits Research thin / NFLX-era misaligned. |
| QuoterV2 at propose | Open — basis used CLI pool proxy; Ops must re-quote before any `strategy propose` |
| Owner “propose now” | Required — this REJECT is not Ops idle forever; it is no live Risk APPROVE yet |

## Cap check
| Sym | bps | ≤3000 |
|-----|-----|-------|
| MSFT | 2800 | Pass |
| GOOGL | 2500 | Pass |
| NVDA | 2000 | Pass |
| AMZN | 1500 | Pass |
| QQQ | 1200 | Pass |

**Cap breaches:** none.

## Live
**FORBIDDEN** until Research patches MSFT/GOOGL/NVDA/QQQ (or owner overrides Critic depth kill **on record**) and Risk re-gates. Paper APPROVE (prior) unchanged. Ops: do **not** propose — no live Risk APPROVE.

## Invert
How we die: shipping $500 into a crowded AI-factor book because the pool haircut got signed while four names still lack Research tables. Basis theater ≠ edge.

## Refs
- Basis: https://github.com/sherwoodagent/grokbot-fund-template/commit/fedcf68da2019669dc5337d0aaf406f78fce5915
- Critic (paper CLEARED): https://github.com/sherwoodagent/grokbot-fund-template/commit/60c94c07ea27c350edc2b0c111ac826818529c68
- Draft: `liveReady true` / `rhBasis OK_WITH_HAIRCUT` / `status live-candidate`

## Handoff
Desk Lead: live REJECT; wait Fund Research patch or owner written depth override → re-gate. Ops: idle (no propose). Scanner: basis file stands.
