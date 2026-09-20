---
asOf: 2026-09-20T13:05:00-05:00
asOfLabel: America/Bogota (COT)
venue: Sherwood RH fork 9994663
mandate: value · PortfolioStrategy · ≤5 names · ≤3000 bps single · 7d
rhBasis: UNKNOWN
liveReady: false
status: OPTIONS ONLY — owner chooses; desk does not pick a winner
eligibleOnly: [AAPL, TSLA, NVDA, MSFT, AMZN, AMD, SPY, QQQ, GOOGL]
excluded: [META, SLV]
---

# Basket OPTIONS — Grok Fund Beta (RH fork widen + score)

Paper / research options for owner selection. Weights in **bps** (sum **10000**). All symbols from **ELIGIBLE** fork registry only. Scored on **Fri session activity** + **X sentiment** under a **value** mandate (quality / cash engines / fair price — not pure momentum chase).

**Shared kill / gate:** `rhBasis: UNKNOWN` · `liveReady: false` → do not execute live until fork mids vs cash basis are measured. Kill any option if live basis prints adverse beyond Risk tolerance, or if a name’s fork route fails (esp. AMD `v4:10000:200` only).

---

## Option 1 — Mega Quality Core

| Symbol | Weight (bps) |
|--------|-------------|
| MSFT | 2800 |
| AAPL | 2500 |
| AMZN | 2200 |
| GOOGL | 2500 |
| **Sum** | **10000** |

- **Activity:** Fri tape soft-to-flat on MSFT/AAPL while AMZN/GOOGL held green — favors buying **quality on softness**, not chasing AMD’s +2.7%.
- **Sentiment (X):** Weekend mega-cap / AI-cash-engine rotation; MSFT Azure+backlog and GOOGL setup posts dominate constructive discourse without requiring semi chase.
- **Value flavor:** Pay fair for durable cash/compounders (cloud + devices + ads/search) rather than PEG-trap semis.

**Risks / kill:** Crowded mega correlation; CapEx/FCF drag on AMZN/MSFT if next prints decelerate. **Kill** if any single name’s fork mid basis vs cash > Risk limit, or if Mon open gaps all four >3% without thesis change.

---

## Option 2 — Index-Anchored Barbell

| Symbol | Weight (bps) |
|--------|-------------|
| SPY | 3000 |
| QQQ | 2200 |
| MSFT | 2000 |
| AAPL | 1500 |
| AMZN | 1300 |
| **Sum** | **10000** |

- **Activity:** SPY ~flat / QQQ +0.6% Fri — index sleeve absorbs narrow Nasdaq leadership without over-concentrating in single-name chip bounce.
- **Sentiment (X):** QQQ “bullish into Q4” chart chatter + mega quality; indexes as ballast while single names stay value-sized.
- **Value flavor:** Beta control via SPY/QQQ; single-name sleeve is quality compounders only (no AMD/TSLA chase).

**Risks / kill:** ETF token liquidity/route risk on fork; QQQ must stay on `v4:3000:60` (CLI warns empty 10000/200 pool). **Kill** if QQQ/SPY fork deviation spikes or if Risk rejects ETF sleeves for this beta.

---

## Option 3 — AI Delivery (anchored, not chase)

| Symbol | Weight (bps) |
|--------|-------------|
| MSFT | 2800 |
| GOOGL | 2500 |
| NVDA | 2000 |
| AMZN | 1500 |
| QQQ | 1200 |
| **Sum** | **10000** |

- **Activity:** NVDA +1.3% / QQQ +0.6% Fri show AI complex bid; MSFT soft print is the **value entry** into the same theme.
- **Sentiment (X):** Strong NVDA/GOOGL/MSFT AI discourse; PEG-trap posts argue against treating cheap semis as automatic buys — hence NVDA capped at 2000 bps, **AMD omitted**.
- **Value flavor:** Own AI via cloud/search cash engines + measured NVDA; refuse AMD Fri spike chase.

**Risks / kill:** NVDA event/vol into next earnings; AI narrative fade. **Kill** if NVDA fork basis widens, or if owner requires zero semi beta.

---

## Option 4 — Soft-Tape Compounders + Beta

| Symbol | Weight (bps) |
|--------|-------------|
| MSFT | 3000 |
| AAPL | 2500 |
| GOOGL | 2000 |
| SPY | 1500 |
| TSLA | 1000 |
| **Sum** | **10000** |

- **Activity:** MSFT (−0.8%) / AAPL (−0.3%) / TSLA (−0.5%) were Fri soft names — value lens prefers **weakness in quality** over strength in AMD.
- **Sentiment (X):** MSFT/GOOGL constructive fundamentals/setup posts; TSLA quieter in value feeds — sized as **small satellite** only.
- **Value flavor:** Max MSFT at mandate cap; AAPL/GOOGL quality; SPY ballast; TSLA optional high-beta toe-hold, not thesis core.

**Risks / kill:** TSLA regulatory (Cybercab/NHTSA) + rates; fashion-multiple risk. **Kill TSLA leg** (or whole option) if NHTSA/AQ headlines worsen or TSLA gaps >5% adverse Mon; also kill if rhBasis for TSLA prints wide.

---

## Option 5 — Semi Satellite + Quality Cage

| Symbol | Weight (bps) |
|--------|-------------|
| MSFT | 2500 |
| GOOGL | 2200 |
| NVDA | 1800 |
| AMD | 1500 |
| SPY | 2000 |
| **Sum** | **10000** |

- **Activity:** Captures Fri **AMD +2.7% / NVDA +1.3%** leadership **without** letting semis dominate (combined 3300 bps; AMD only 1500).
- **Sentiment (X):** AMD Piper/demand posts hot; counterweighted by PEG-trap warnings → treat AMD as **satellite**, MSFT/GOOGL/SPY as cage.
- **Value flavor:** Explicit anti-chase sizing — activity acknowledged, weights refuse momentum monopoly.

**Risks / kill:** AMD route is **`v4:10000:200` only** (no fee-3000); higher fee / thinner book. **Kill** if AMD quote fails, feedDeviation blows out, or Fri strength mean-reverts hard Mon without MSFT/GOOGL offset.

---

## Owner decision

Pick **one** option (or request a merge). Desk Lead / PM will draft `proposals/` only after owner choice + Risk gate. **No winner selected by Scanner.**
