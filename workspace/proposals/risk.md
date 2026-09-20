# Risk note — Owner basket 3 LIVE RE-GATE (basis upgrade)
lens:munger

## Decision
**REJECT** (live-candidate) — unchanged

## Reason
Live V4 quoter cleared RH basis (`rhBasis: OK`, max |pool−cash| ~53 bps GOOGL) — Critic live-depth kill still open (Research missing on MSFT/GOOGL/NVDA/QQQ); no live APPROVE on thin thesis.

## Basis upgrade (logged)
| Prior | Now |
|-------|-----|
| CLI `feedDeviationBps` NVDA ~199 / AMZN ~264 (stale) | Live Uniswap **v4** Quoter — all five OK |
| `rhBasis` OK_WITH_HAIRCUT | **`rhBasis: OK`** (bf80141 / efc176e) |
| Haircut material | Haircut **not** required for basis gate anymore |

Owner haircut record can stand as history; it is no longer the live blocker.

## What cleared
| Item | Status |
|------|--------|
| Caps ≤5 / ≤3000 bps | Pass — MSFT 2800 max |
| Kill criteria present | Pass |
| RH basis (live V4) | **Pass — OK** · max \|pool−cash\| 53.2 bps (GOOGL) |
| Opaque calldata / Privy skip | N/A — not an Ops propose |

## What still blocks live
| Item | Status |
|------|--------|
| Research depth (MSFT/GOOGL/NVDA/QQQ) | **Fail** — only remaining live veto. Critic CLEARED paper only; draft still admits Research thin. |
| Size-aware re-quote at propose | Open — 100 USDG probe ≠ fill; Ops re-quotes if/when live APPROVE + owner “propose now” |
| Owner “propose now” | Still required after any future live APPROVE |

## Cap breaches
None.

## Live
**FORBIDDEN** until Research patches MSFT/GOOGL/NVDA/QQQ (or owner overrides Critic depth kill **on record**) and Risk **re-gates**. Do **not** treat this note as live APPROVE. Ops: idle.

## Invert
How we die: mistaking a clean V4 basis print for thesis depth. Edge still unproven on four names.

## Refs
- Basis upgrade: `bf80141` + `efc176e` (live V4 quoter → `rhBasis: OK`)
- Prior REJECT: https://github.com/sherwoodagent/grokbot-fund-template/commit/173b1dca0fd8fd37ebdd610228fed4fc6acc0977
- Critic (paper CLEARED): https://github.com/sherwoodagent/grokbot-fund-template/commit/60c94c07ea27c350edc2b0c111ac826818529c68

## Handoff
Desk Lead: noted — basis OK; REJECT stands on Research depth only. Re-gate when Research patch lands or owner depth override on record. Ops: hold.
