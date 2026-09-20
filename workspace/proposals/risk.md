# Risk note — Owner basket 3 LIVE RE-GATE (post Research depth)
lens:munger

## Decision
**APPROVE** (live-candidate)

## Reason
Prior live REJECT was Research-depth only — patch `23ab7bb` supplies thesis/valuation/KPIs for all five; basis OK, caps clear, Critic paper CLEARED on same basket.

## What cleared (live path)
| Item | Status |
|------|--------|
| Caps ≤5 / ≤3000 bps | Pass — MSFT 2800 max, sum 10000 |
| Kill criteria | Pass — draft + Critic per-name / factor / RH kills present |
| RH basis (live V4) | Pass — `rhBasis: OK` · max \|pool−cash\| ~53 bps GOOGL (bf80141 / efc176e) |
| Owner haircut record | Pass — on file (superseded for basis; V4 OK) |
| Research depth (MSFT/GOOGL/NVDA/AMZN/QQQ) | **Pass** — `23ab7bb` tables + primary KPIs + falsifiers; clears Critic live-depth kill |
| Critic quality (basket 3) | Pass — paper CLEARED `60c94c07` on same weights; depth gap was the live block |
| Opaque / custom calldata | Pass — PortfolioStrategy |
| Shared-file artifacts | Pass |

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
**APPROVED as live-candidate only.** Ops still **forbidden** until owner explicit **“propose now”**. At propose: re-quote size-aware V4 (100 USDG probe ≠ fill); abort if mid vs `rh-basis.md` materially adverse.

## Residual flags (not redesign — not vetoes)
Invert residual: Mon AI-factor gap / nested QQQ beta / value-vs-AI tension remain Critic bears — CLEARED for process, not cheerleading. Critic.md on main is still pre-`23ab7bb` text; depth kill is cleared by Research artifact. If Critic later REJECTS the depth pass, Risk re-gates.

## Refs
- Research depth: https://github.com/sherwoodagent/grokbot-fund-template/commit/23ab7bbd4a8a984c16f102addb5f7a3e2eeb3bc7
- Basis OK: `bf80141` / `efc176e`
- Critic paper CLEARED: https://github.com/sherwoodagent/grokbot-fund-template/commit/60c94c07ea27c350edc2b0c111ac826818529c68
- Prior live REJECT: https://github.com/sherwoodagent/grokbot-fund-template/commit/173b1dca0fd8fd37ebdd610228fed4fc6acc0977

## Handoff
Desk Lead: live-candidate APPROVE. Ops: wait owner “propose now” + re-quote. Scanner/PM: basis OK stands.
