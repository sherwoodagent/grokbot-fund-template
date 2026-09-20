# Risk Gate — Sherwood Fund Desk (Paper Loop II)

**Role:** Risk  
**Persona / tag:** `lens:munger`  
**Dated:** Sunday, Sep 20, 2026 (America/Bogota, UTC-5)  
**Inputs:** `workspace/proposals/draft.json` · `workspace/briefs/critic.md` · `workspace/mandate.md`  
**Loop:** Second pass after Research patch; Critic quality CLEARED, theses still bearish; PM no-trade.

**Disclaimer:** Stylized *munger* checklist (invert; hard vetoes). Not Charlie Munger.

---

## Decision

**REJECT**

**One-line reason:** No live Ops — quality CLEARED but empty basket / liveReady false / RH basis UNKNOWN; nothing to size and no APPROVE path for Ops.

---

## Invert checklist (hard vetoes)

| Veto | Status | Note |
|------|--------|------|
| Opaque calldata | Pass (N/A) | No trade / no calldata proposed |
| Single name > max weight (30%) | Pass | `weights: []` — no name sized |
| > max names (5) | Pass | Empty basket |
| Missing kill criteria | Pass | Draft carries NFLX / AMZN / RH / desk kills |
| Ops skipping Privy discipline | Pass (N/A) | Live Ops forbidden without APPROVE; not invoked |
| Trust-me without artifacts | Pass (quality) | Critic CLEARED paper-loop quality after Research patch |
| Live-ready without measured RH basis | **Fail (hard)** | `rhBasis: UNKNOWN` + `liveReady: false` — do not size for live |
| Approve empty / no-trade as Ops | **Fail (hard)** | Status `no-trade`, empty weights — nothing to execute |

Empty basket is **not** a live proposal. Hard gate: do **not** APPROVE for Ops. Default on ambiguity: REJECT.

---

## Cap breaches

None. Empty weights comply with mandate caps (≤5 names, ≤30% single-name, PortfolioStrategy / value / 7d).

---

## Mandate / desk alignment

- Venue RH fork `9994663` · PortfolioStrategy only · value · max 5 · 30% · 7d — draft status `no-trade` with empty weights stays inside caps.
- Critic quality CLEARED; thesis stance still reject longs (NFLX high-confidence wrong; AMZN med on entry). PM correctly issued no-trade rather than invent weights.
- Vault USDG balance (if any) is irrelevant — this is **not** an Ops request; Risk does not green-light live on cash alone.
- `lens:munger`: invert — what would make us look foolish? Approving Ops into UNKNOWN RH basis with zero basket. Avoid that.

---

## Handoff

- **Ops / chain:** Do not touch. Live Ops forbidden without Risk APPROVE.
- **PM:** Correct no-trade this loop; re-open only if a falsifiable positive-asymmetry name appears with measured RH basis.
- **Research (optional next):** If desk wants a name, hunt a different value candidate — current NFLX/AMZN soft-sells still fail Critic’s 7d edge test; do not re-force those longs.
- **Desk Lead:** Gate closed — REJECT (no live / no basket).

`lens:munger`
