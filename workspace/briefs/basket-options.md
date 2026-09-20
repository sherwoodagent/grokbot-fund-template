---
asOf: 2026-09-20T13:05:00-05:00
asOfLabel: America/Bogota (COT)
venue: Sherwood RH fork 9994663
mandate: value · PortfolioStrategy · ≤5 names · ≤3000 bps single · 7d
rhBasis: UNKNOWN
liveReady: false
status: OPTIONS ONLY — owner chooses; Research does not pick a winner
eligibleOnly: [AAPL, TSLA, NVDA, MSFT, AMZN, AMD, SPY, QQQ, GOOGL]
excluded: [META, SLV, PLTR, NFLX]
scan: https://github.com/sherwoodagent/grokbot-fund-template/commit/817f9be677f722966d6595e8eda50234370ed38f
watchlist: https://github.com/sherwoodagent/grokbot-fund-template/commit/c7ad7d5506d9ba57e8b4708df6b6ab8471491807
---

# Basket OPTIONS — widen pass (empty basket rejected)

Paper options for **owner** selection. Weights in **bps** (must sum **10000**; single ≤ **3000**). Symbols from **eligible** fork universe only. Scored on Fri session activity + X sentiment under a **value** mandate (quality / cash engines / fair price — not pure momentum chase).

**Shared gate:** `rhBasis: UNKNOWN` · `liveReady: false` → do **not** go live until fork mids vs cash basis are measured. Kill any option if live basis prints adverse beyond Risk tolerance.

---

## Option 1 — Mega Quality Core

| Symbol | Weight (bps) |
|--------|-------------|
| MSFT | 2800 |
| AAPL | 2500 |
| GOOGL | 2500 |
| AMZN | 2200 |
| **Sum** | **10000** |

- **Activity:** Fri soft-to-flat on MSFT (−0.8%) / AAPL (−0.3%) while AMZN (+1%) / GOOGL (+0.6%) held — favors buying **quality on softness**, not chasing AMD’s +2.7%.
- **Sentiment (X):** Mega-cap / AI-cash-engine rotation; MSFT + GOOGL constructive without requiring semi chase.
- **Value flavor:** Pay fair for durable cash/compounders (cloud + devices + ads/search).

**Risks / kill:** Crowded mega correlation; CapEx/FCF drag if next prints decelerate. **Kill** if fork mid basis vs cash > Risk limit, or Mon open gaps all four >3% without thesis change.

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

- **Activity:** SPY −0.12% / QQQ +0.63% Fri — index sleeve absorbs narrow Nasdaq leadership without over-concentrating in single-name chip bounce.
- **Sentiment (X):** QQQ vs broad / concentration chatter; indexes as ballast while single names stay value-sized.
- **Value flavor:** Beta control via SPY/QQQ; single-name sleeve is quality compounders only (no AMD/TSLA chase).

**Risks / kill:** ETF token liquidity/route risk on fork. **Kill** if QQQ/SPY fork deviation spikes or Risk rejects ETF sleeves.

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
- **Sentiment (X):** NVDA earnings/AI demand dominant; GOOGL relative-strength / Waymo notes in soft AI tape — NVDA capped at 2000 bps, **AMD omitted**.
- **Value flavor:** Own AI via cloud/search cash engines + measured NVDA; refuse AMD Fri spike chase.

**Risks / kill:** NVDA event/vol; AI narrative fade. **Kill** if NVDA fork basis widens, or if owner requires zero semi beta.

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
- **Sentiment (X):** MSFT/GOOGL constructive; TSLA quieter — sized as **small satellite** only.
- **Value flavor:** Max MSFT at mandate cap; AAPL/GOOGL quality; SPY ballast; TSLA optional high-beta toe-hold, not thesis core.

**Risks / kill:** TSLA regulatory / rates; fashion-multiple risk. **Kill TSLA leg** (or whole option) if Cybercab/NHTSA headlines worsen or TSLA gaps >5% adverse Mon; also kill if rhBasis for TSLA prints wide.

---

## Option 5 — Semi Satellite + Quality Cage

| Symbol | Weight (bps) |
|--------|-------------|
| MSFT | 2500 |
| GOOGL | 2200 |
| SPY | 2000 |
| NVDA | 1800 |
| AMD | 1500 |
| **Sum** | **10000** |

- **Activity:** Captures Fri **AMD +2.7% / NVDA +1.3%** leadership **without** letting semis dominate (combined 3300 bps; AMD only 1500).
- **Sentiment (X):** AMD bounce / semiconductor rebound hot; counterweighted by value mandate → treat AMD as **satellite**, MSFT/GOOGL/SPY as cage.
- **Value flavor:** Explicit anti-chase sizing — activity acknowledged, weights refuse momentum monopoly.

**Risks / kill:** AMD route/liquidity thinner on fork; mean-reversion after Fri spike. **Kill** if AMD quote fails, feedDeviation blows out, or Fri strength mean-reverts hard Mon without MSFT/GOOGL offset.

---

## Owner decision

Pick **one** option (or request a merge). PM drafts proposal only after owner choice + Risk gate. **Research does not select a winner.**
