# Risk Gate — Sherwood Fund Desk (Paper Loop)

**Role:** Risk  
**Persona / tag:** `lens:munger`  
**Dated:** Sunday, Sep 20, 2026 (America/Bogota, UTC-5)  
**Inputs:** `workspace/proposals/draft.json` · `workspace/briefs/critic.md` · `workspace/mandate.md`

**Disclaimer:** Stylized *munger* checklist (invert; hard vetoes). Not Charlie Munger.

---

## Decision

**REJECT**

**One-line reason:** No live Ops — Critic quality REJECT + PM no-trade empty basket; Research must patch valuation/RH basis/KPIs before any re-entry.

---

## Invert checklist (hard vetoes)

| Veto | Status | Note |
|------|--------|------|
| Opaque calldata | Pass (N/A) | No trade / no calldata proposed |
| Single name > max weight (30%) | Pass | `weights: []` — no name sized |
| > max names (5) | Pass | Empty basket |
| Missing kill criteria | Pass | Draft carries NFLX / AMZN / desk-quality kills |
| Ops skipping Privy discipline | Pass (N/A) | Live Ops forbidden without APPROVE; not invoked |
| Trust-me without artifacts | **Fail (inherited)** | Critic quality-REJECT’d Research (no valuation math, no RH vs US-cash basis, thin primary KPIs, asymmetry to Wells $57 unaddressed) |

Empty basket is **not** a live proposal. Hard gate: do **not** APPROVE for Ops.

---

## Cap breaches

None. Empty weights comply with mandate caps (≤5 names, ≤30% single-name, PortfolioStrategy / value / 7d).

---

## Mandate / desk alignment

- Venue RH fork `9994663` · PortfolioStrategy only · value · max 5 · 30% · 7d — draft status `no-trade` with empty weights stays inside caps.
- PM correctly bounced to Research after Critic quality REJECT; Risk formalizes: **no APPROVE path this loop**.
- Default on ambiguity: REJECT. Ambiguity here is resolved against live Ops.

---

## Handoff

- **Ops / chain:** Do not touch. Live Ops forbidden without Risk APPROVE.
- **Research:** Patch Critic gaps (valuation table, RH fork basis vs US cash, primary KPI support) before PM re-sizes.
- **Desk Lead:** Gate closed — REJECT (no live).

`lens:munger`
