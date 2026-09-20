---
rhBasis: UNKNOWN
liveReady: false
asOf: 2026-09-20T12:49:39-05:00
asOfLabel: America/Bogota (COT)
sessionNote: US cash markets closed (Sunday); movers = Fri 2026-09-18 regular session (Yahoo Finance chart)
venue: Sherwood RH fork chainId 9994663
watchlist: [TSLA, AMZN, PLTR, NFLX, AMD]
---

# Scan brief — 2026-09-20 (Sunday, America/Bogota)

## Status / data gaps

- **rhBasis: UNKNOWN** — desk `fund.json` has `chainId: 9994663` but empty `rpc` / `vault`; RH assets API lists watchlist token deployments only on mainnet **4663**, not fork **9994663**. No reliable **fork** mids available → cannot compare RH-fork mid vs US cash.
- **liveReady: false** — do not size or submit live PortfolioStrategy until fork mids are queryable and basis is checked.
- DexScreener `chainId: robinhood` (mainnet 4663) had partial books for PLTR/NFLX/AMD earlier this scan window; those are **not** fork quotes and were rate-limited for TSLA/AMZN. Ignored for rhBasis.
- X search API unavailable this run (client-not-enrolled); narratives from public news only.

## Top movers (watchlist only)

Source: **Yahoo Finance** `v8/finance/chart`, Fri **2026-09-18** close vs prior daily close. Weekend — no fresh cash prints.

| Sym | Last (Fri) | Prior close | Δ % | Note |
|-----|------------|-------------|-----|------|
| **NFLX** | $71.79 | $75.31 | **-4.67%** | Largest downside; Wells Fargo UW cut |
| **AMD** | $559.82 | $545.09 | **+2.70%** | Largest upside; AI/supply narrative |
| AMZN | $253.71 | $251.19 | +1.00% | Mega-cap support Friday |
| PLTR | $177.64 | $176.24 | +0.79% | Quiet tape; contract headlines |
| TSLA | $364.27 | $366.20 | −0.53% | Flat; Cybercab/rates overhang |

**Ranked movers:** NFLX (−4.67%), AMD (+2.70%), then AMZN / PLTR / TSLA in a sub-1% band.

## Narrative spikes (watchlist only)

- **NFLX — Wells Fargo Underweight / PT $57:** Rare downgrade from Equal Weight; engagement + 2H26 content-slate concerns; Friday −4.7% drag on comms.  
  https://www.marketscreener.com/news/wells-fargo-downgrades-netflix-to-underweight-from-equalweight-adjusts-price-target-to-57-from-80-ce785adadb88f12d  
  https://ftinvest.us/2026/09/19/chip-rally-fuels-late-recovery-as-nasdaq-ends-week-in-positive-territory/

- **AMD — supply squeeze / Street $600+ targets:** Management demand-ahead-of-supply; Piper Overweight $600; tape extended but Friday +2.7% on chip rally.  
  https://blockchain.news/news/20260919-price-prediction-amd-supply-squeeze-600-targets-but-the  
  https://ftinvest.us/2026/09/19/chip-rally-fuels-late-recovery-as-nasdaq-ends-week-in-positive-territory/

- **PLTR — $48M Army ammo contract + OperatorOS aviation:** USG ammo-management award (~$48.1M); Surf Air OperatorOS first commercial (powered by Palantir). Stock only +0.8% Fri.  
  https://www.gate.com/news/detail/PLTR/palantir-lands-48m-us-army-contract-for-ammunition-management-overhaul-24386462  
  https://marketchameleon.com/articles/b/2026/9/18/pltr-operatoros-surf-air-mobility-sprintbach-first-commercial-contract

- **AMZN — Jassy / AWS–Nvidia framing:** Jassy: customers will run on Nvidia “for as long as we can foresee”; Friday +1% with mega-caps. Lower edge vs NFLX/AMD movers.  
  https://www.fool.com/investing/2026/09/19/these-15-words-from-amazon-s-andy-jassy-may-eliminate-nvidia-s-biggest-risk/

- **TSLA — Cybercab NHTSA AQ + yields:** NHTSA Audit Query AQ26002 on Cybercab FMVSS self-cert; shares ~$364, below post-earnings $374, rates pressure.  
  https://www.nhtsa.gov/press-releases/investigation-tesla-cybercab-self-certification  
  https://xinvestnews.com/news/tesla-stock-holds-below-374-as-5-treasury-yields-pressure-robotaxi-valuation/

- **ARK trim (AMD + PLTR):** Ark Innovation sold PLTR/AMD (profit-taking framing) vs ACHR buys — secondary flow story, not a primary tape driver Sunday.  
  https://www.fool.com/investing/2026/09/19/cathie-wood-sold-palantir-and-amd-then-poured-335/

## Names to ignore today

- **TSLA** — sub-1% Fri move; regulatory/yield narrative is known; no fresh weekend cash catalyst for a value-flavor 7d basket edge without rhBasis.
- **AMZN** — modest +1%; narrative is Nvidia/AWS color, not a clear under/overvalued setup for this desk without fork mid.
- **PLTR** — headlines constructive but price barely moved (+0.8%); valuation still rich; no RH-fork basis to lean on.

**Focus candidates if/when liveReady flips:** NFLX (event downgrade / dislocation) and AMD (momentum + supply narrative) — still **research-only** until `rhBasis: OK`.

## Mandate reminder (no weights)

- PortfolioStrategy only, value flavor, max 5 names, max 30% single, 7d default.
- Forbidden: opaque calldata, memecoins, unlisted venues.
- Venue = RH fork **9994663** only — mainnet 4663 Dex quotes do not clear liveReady.

## Blockers for Desk Lead / Risk

1. Populate `fund.json` `rpc` (and vault) for chain **9994663**, or otherwise expose fork token mids for TSLA/AMZN/PLTR/NFLX/AMD.
2. Re-run mid vs cash basis; only then may Scanner set `rhBasis: OK` and `liveReady: true`.
3. Cash market closed until Mon open America/New_York — weekend briefs stay on Fri session prints.
