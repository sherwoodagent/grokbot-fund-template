# Critic Brief — MorphoSupply S1 re-stamp (Burry)

**Role:** Critic (`lens:burry`)  
**Dated:** Thursday, Sep 24, 2026 ~13:26 America/Bogota (UTC-5)  
**Scope:** **S1 ONLY** — MorphoSupply live after proposal #2 settles  
**Owner:** Carlos GO Morpho S1 (Desk Lead route)  
**Prior Critic:** BLOCKED (no market ID) — **superseded** for that gap  
**Inputs:** `ops/morpho-cl-cookbook.md` · `ops/morpho-init-dryrun.md` · `ops/status.md` · `briefs/satellite-options.md` · `mandate.md`  
**Hard rule:** Proposal **#2 Option B** equities [AAPL/MSFT/AMZN/SPY] — **LEAVE ALONE** (Executed; settle still needs GO). Do not reweight, unlock, or conflict.

**Disclaimer:** Stylized burry checklist — not Michael Burry.

---

## VERDICT: **CLEARED** (conditional)

**CLEARED for MorphoSupply S1 live propose **only after** proposal #2 is **Settled**** — sequential satellite, not parallel to live #2.

Prior **BLOCKED** (missing market ID) is **lifted**: Ops verified Morpho Blue + flagship market + TierRegistry Morpho standing + **init dry-run PASS**.

**Not CLEARED as yield alpha.** Flagship market is near-idle (~0.04% util · ~0.00024% supply APY). This is a **USDG float park** between equity books, not a cash-engine sleeve. Size for liquidity parking — do not chase APY that isn’t there. `lens:burry`

---

## Locked venue (single market)

| Field | Value |
|-------|-------|
| Class | **MorphoSupplyStrategy** |
| Morpho Blue | `0x9D53d5E3bd5E8d4Cbfa6DB1ca238AEA02E651010` |
| Template | `0x5B55E1Da361573CB0788e750038567D2569BE41d` (approved) |
| **marketId** | `0xdeb4782d012d5fd3b24962538c2f6559049d70bda4dabd2e4212dacb96c28d45` |
| loanToken | USDG `0x5fc5360D…d168` |
| collateralToken | AAPL `0xaF3D76f1…93f9` (borrower collateral — sleeve supplies USDG only) |
| LLTV | 62.5% |
| Init dry-run | **PASS** (`cloneAndInit` eth_call / Tenderly / trace — dust 1 USDG) |

**Any other marketId** (TSLA, USDe, BIGTECH 100% util, etc.) → **not** this CLEARED — needs a fresh Critic stamp.

---

## Caps (hard)

| Cap | Critic |
|-----|--------|
| Timing | Propose **only after #2 Settled** (not while Executed + settle HOLD) |
| Capital | **≤25%** of vault for **first** live Morpho sleeve (tighter than mandate 40% — idle APY + no execute/settle dry-run yet) |
| Mandate ceiling | Still ≤40% satellite if owner overrides after one clean settle cycle |
| Market | **100%** of this proposal into flagship USDG/AAPL market only (template = single-market) |
| Path | Manual `StrategyFactory.cloneAndInit` + governor batches OK (no CLI) — Ops owns encoding; Risk verifies calldata match cookbook |
| Expectation | Treat expected return ≈ **0** over 7d; CLEARED for **float / settle path proof**, not for yield |

---

## What Ops cleared vs what Critic still owns

| Gate | Status |
|------|--------|
| Market ID named + keccak match | **Cleared** (Ops) |
| Morpho `isCounterpartyAllowed` | **Cleared** (Ops) |
| Template approved + init dry-run | **Cleared** (Ops) |
| Execute (approve + supply) dry-run | **Not done** — Critic kill if first live execute fails |
| Settle (withdraw-by-shares) dry-run | **Not done** — Critic kill if `SettlementIncomplete` / util lock |
| CLI `morpho-supply` | **Absent** — manual only; not a thesis BLOCK if Ops executes cookbook path |

---

## Bear bullets (≤6)

1. **APY is theater:** ~0.00024% is not a sleeve thesis — if PM sells “Morpho yield,” that’s folklore. `lens:burry`
2. **Init ≠ execute/settle:** Dry-run stopped at clone. First live risk is supply + exit, not init. `lens:burry`
3. **Utilization lock:** Idle today; a borrow spike can block full settle → residue/`sweep` path must work. `lens:burry`
4. **Oracle / market integrity:** Supplier doesn’t hold AAPL, but market health depends on oracle + borrower collateral — AAPL oracle incident → bad-debt / lock risk. `lens:burry`
5. **Manual propose surface:** No CLI = higher Ops error risk (wrong marketParams / amount). Calldata must match cookbook hex layout. `lens:burry`
6. **Sequence discipline:** Proposing S1 while #2 still Executed violates one-live-proposal + post-settle rule — process kill. `lens:burry`

---

## Explicit kill criteria

| Kill | Condition |
|------|-----------|
| **Timing** | Any S1 propose / broadcast **before #2 Settled** → **process kill** |
| **Market** | Propose uses any marketId ≠ `0xdeb4782d…8d45` without new Critic stamp → **kill** |
| **Init/execute** | Live `cloneAndInit` or execute reverts / wrong Morpho binding → abort; no retry-spam |
| **Settle** | `SettlementIncomplete` with material residue **or** util prevents exit beyond Risk tolerance → early escalate / sweep; do not roll size up |
| **Oracle / security** | AAPL Morpho oracle stale/incident **or** Morpho Blue exploit / pause → **kill sleeve**; settle ASAP if possible |
| **APY bait** | Draft that sizes >25% *because of* “yield” while util stays ~0 → **reject sizing rationale** |
| **#2 conflict** | Any attempt to mid-reweight or settle #2 solely to unlock S1 without owner settle GO → **refuse** |

---

## WHAT COULD I BE WRONG ABOUT?

1. **Utilization wakes up** after we supply — APY becomes real and ≤25% was too timid for mandate Priority 2 float.
2. **Execute + settle are boring on first live** — init dry-run quality carries through; Critic over-weighted “not simulated” residual.
3. **Idle market is a feature** — easy exit forever; then float parking at mandate 40% is safer than I capped.
4. **CLI arrives soon** — manual-path risk shrinks; process kills around encoding become less binding.
5. **AAPL oracle is rock-solid on this fork** — collateral-leg risk is overstated for a pure USDG supplier.
6. **#2 never settles on owner timeline** — CLEARED-after-settle ages into a dead GO; desk needs an explicit expire/reconfirm.
7. **Wrong flagship** — USDe or another USDG market would have been better float; AAPL market stays forever dead and we learn nothing.

---

## Confidence bull wrong

| Scope | Confidence | Note |
|-------|------------|------|
| Market-ID BLOCK still valid | **High** it’s **wrong now** — gap closed |
| S1 as yield sleeve | **High** bull wrong — APY ~0 |
| S1 as float / path proof post-#2 Settled | **Med** | CLEARED with caps |
| First-live execute/settle smooth | **Med** residual risk | No dry-run yet |

---

## One-line

**CLEARED Morpho S1 live after #2 Settled — flagship USDG/AAPL marketId `0xdeb4782d…`; ≤25% first sleeve; float not yield; #2 equities untouched.**

---

## Handoff

- **Lead / PM:** Queue S1 only post-#2 Settled; size ≤25% first pass; cookbook market only.  
- **Risk:** Verify calldata = dry-run layout; require settle path understanding (`SettlementIncomplete` / sweep).  
- **Ops:** Manual clone path; no CLI; do not touch #2.  
- **Critic scope ends** (S1 only — S2/S3/S4 not re-opened here).
