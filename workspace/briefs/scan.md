---
rhBasis: UNKNOWN
liveReady: false
asOf: 2026-09-20T13:02:00-05:00
asOfLabel: America/Bogota (COT)
sessionNote: US cash closed (Sunday); movers = Fri 2026-09-18 Yahoo Finance chart (close vs prior close)
venue: Sherwood RH fork chainId 9994663
universe: [AAPL, TSLA, NVDA, MSFT, AMZN, AMD, SPY, QQQ, GOOGL]
excluded: [META (feed/pool), SLV (v3), PLTR (not on CLI fork-tokens), NFLX (not on CLI fork-tokens)]
---

# Scan brief — widened fork universe — 2026-09-20 (Sunday)

## Status / rhBasis

- **rhBasis: UNKNOWN** — no RH-fork (`9994663`) token mids vs US cash. RH assets API lists deployments on mainnet **4663** only; desk `fund.json` still has empty `rpc`/`vault` for the fork.
- **liveReady: false** — empty basket was owner-rejected; do **not** size until fork mids are queryable and basis is OK.
- Universe = CLI `fork-tokens` eligible stock tokens on fork **9994663**: AAPL TSLA NVDA MSFT AMZN AMD SPY QQQ GOOGL.
- **Dropped:** META (feed/pool), SLV (v3), PLTR + NFLX (not on CLI fork registry this pass — mainnet 4663 listings do **not** prove fork eligibility).

## Top activity (Fri 2026-09-18, US cash proxy)

Source: Yahoo Finance `v8/finance/chart`, Fri close vs prior daily close.

| Rank | Sym | Last | Prior | Δ % | Role |
|------|-----|------|-------|-----|------|
| 1 | **AMD** | $559.82 | $545.09 | **+2.70%** | Largest upside — semis bid |
| 2 | **NVDA** | $222.27 | $219.34 | **+1.34%** | Semis/AI leadership |
| 3 | **AMZN** | $253.71 | $251.19 | **+1.00%** | Mega-cap support |
| 4 | **MSFT** | $493.78 | $497.75 | **−0.80%** | Softest mega-cap |
| 5 | **GOOGL** | $349.54 | $347.33 | **+0.64%** | Modest bid |
| 6 | **QQQ** | $721.45 | $716.92 | **+0.63%** | Nasdaq proxy green |
| 7 | **TSLA** | $364.27 | $366.20 | **−0.53%** | Drift / failed bounce |
| 8 | **AAPL** | $336.13 | $337.00 | **−0.26%** | Quiet |
| 9 | **SPY** | $761.69 | $762.60 | **−0.12%** | Flat index — narrow tape |

**Tape read:** Semis + mega-cap growth carried QQQ while SPY leaked — classic narrow risk bid ([QAXUS 2026-09-18 PM](https://qaxus.com/intel/markets-2026-09-18-pm)).

### Top activity names (for Desk Lead handoff)
1. **AMD** (+2.70%) — primary activity / supply-AI narrative  
2. **NVDA** (+1.34%) — Jensen guidance / chip-sales-double framing  
3. **AMZN** (+1.00%) — mega-cap bid  
4. **MSFT** (−0.80%) — relative soft / fade candidate among megas  
5. **QQQ** (+0.63%) vs **SPY** (−0.12%) — growth-vs-broad split

## Narrative spikes (eligible only)

- **NVDA / semis complex:** Huang chip-sales-double / ~70% rev growth toward Jan 2028 framing; NVDA +1.3%, PHLX semis firm; after-hours memory rotation noted.  
  https://qaxus.com/intel/markets-2026-09-18-pm  
  https://waverider.ai/market-analysis/market-summary-post-market-2026-09-18/

- **AMD:** Extended AI/supply squeeze narrative; Friday +2.7% on chip rally (largest name in universe).  
  https://exa.ai/library/markets/stock/AMD?date=2026-09-18

- **QQQ vs SPY:** QQQ +0.6% / SPY ~flat — narrow mega-cap + semis tape, weak breadth.  
  https://qaxus.com/intel/markets-2026-09-18-pm

- **TSLA:** Failed gap-up / closed near lows; Goldman Q3 delivery trim (~435k vs ~456k cons) overhang.  
  https://qaxus.com/intel/markets-2026-09-18-pm

- **AMZN:** Mega-cap strength (+1%); Consumer Discretionary propped by AMZN.  
  https://waverider.ai/market-analysis/market-summary-post-market-2026-09-18/

- **AAPL / MSFT / GOOGL:** No single dominant Fri headline in this pass — AAPL quiet (−0.3%), MSFT soft (−0.8%), GOOGL modest (+0.6%).

## Names to deprioritize today

- **SPY** — essentially flat; not an activity name for a value 7d basket edge.
- **AAPL** — sub-0.3% move; no fresh Fri catalyst.
- **TSLA** — known overhang; sub-1% drift; no fork mid for basis trade.

## Mandate / compliance

- PortfolioStrategy · value flavor · max 5 names · max 30% single · 7d default.
- Forbidden: opaque calldata, memecoins, unlisted venues.
- Venue = RH fork **9994663** only. Mainnet 4663 Dex/RH listings ≠ liveReady.
- **No weights** (PM owns sizing).

## Blockers

1. Need fork RPC / fork token mids for the 9 eligible names on **9994663**.
2. Until then: `rhBasis: UNKNOWN`, `liveReady: false` — Research may draft, Ops must not go live.
3. Cash closed until Mon America/New_York open — weekend briefs use Fri prints.
