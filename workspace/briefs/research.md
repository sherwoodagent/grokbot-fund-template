# Research Brief — Bull Case (Paper Loop) — Quality Patch

**Role:** Research (persona lens: `lynch_buffett`)  
**Dated:** Sunday, Sep 20, 2026 (America/Bogota, UTC-5)  
**Session data:** Latest US cash session **Friday, Sep 18, 2026** (weekend — markets closed)  
**Venue / mandate:** Sherwood RH fork (chain `9994663`) · **PortfolioStrategy only** · flavor **value** · max 5 names · 30% max weight · 7d  
**Watchlist:** TSLA, AMZN, PLTR, NFLX, AMD  
**Sizing:** Left to PM — Research does not recommend weights.

**Disclaimer:** This brief applies a *lynch_buffett-style checklist* (circle of competence, durable demand/moat vs fashion, growth without fantasy multiples, fair price over cheap garbage). It does **not** claim personal endorsement by Warren Buffett or Peter Lynch.

**Patch note:** Rewritten to clear Critic quality REJECT (valuation tables, RH basis note, primary KPIs, NFLX asymmetry). Prior brief lacked falsifiable math.

---

## Candidates (1–3; value flavor)

| Priority | Ticker | One-line thesis |
|----------|--------|-----------------|
| 1 | **NFLX** | Soft-sell after Wells Fargo Underweight / Street-low **$57** PT may be a *fair-price* entry into a still-understandable streaming franchise — **only if** engagement holds and RH basis is later known; asymmetry to $57 is real (~20%+). |
| 2 | **AMZN** | Durable retail + AWS cash engine with a narratable moat; Fri modest bid is **not** a soft-sell dislocation — quality compounder at a *fair* (not distressed) multiple, subject to RH basis haircut. |

**Passed for this pass (not candidates):**  
- **AMD** — Fri strength on unconfirmed ~10% channel hike; value lens: do not chase. `lens:lynch_buffett`  
- **TSLA** — Robotaxi/Optimus + negative FCF closer to fashion/fantasy multiple. `lens:lynch_buffett`  
- **PLTR** — Quiet Fri tape; weekend UBS AI-pick note is narrative, not a soft-sell. `lens:lynch_buffett`

---

## Valuation table (US cash proxy — Fri Sep 18, 2026)

Prices from Exa Markets session closes (same source family as scan). Multiples from secondary market-data aggregators cited below — **not** fabricated 10-Q text. EV where unavailable is marked.

### NFLX

| Field | Value | Source |
|-------|-------|--------|
| Spot (Fri close) | **$71.78** (−4.69% vs **$75.31** prior) | [Exa NFLX 2026-09-18](https://exa.ai/library/markets/stock/NFLX?date=2026-09-18) |
| Market cap | ~**$300–307B** (provider range; Signals ~$306.8B near $72) | [Signals.AI NFLX](https://signals.ai/symbol/nflx); [Invest Insider](https://investinsidernews.com/stock-market/stock-market-today-sept-18-netflix-falls-on-analyst-downgrade-and-slashed-price-target/) (~$299B) |
| EV | **Not retrieved** this pass (honest gap) | — |
| Trailing P/E | ~**22.0–23.7×** | Signals ~22.5; GuruFocus ~23.73 |
| FCF yield | ~**3.6–3.7%** (TTM) | [Signals.AI](https://signals.ai/symbol/nflx) |
| P/S | ~**6.2–6.3×** | Signals |
| Wells Fargo PT | **$57** (Underweight; cut from **$80**) | [GuruFocus](https://www.gurufocus.com/news/9088313/netflix-drops-48-as-wells-fargo-cuts-its-target-to-57); [ECM Source](https://ecmsource.com/netflix-downgraded-underweight-wells-fargo-target-57-september-2026/) |

**Wells $57 vs spot — downside math (required):**  
- Spot **$71.78** → Wells **$57**: downside = $(71.78 − 57) / 71.78 ≈ **20.6%**.  
- Spot is still ~**25.9%** *above* Wells PT ($71.78 / $57 − 1).  
- From prior close **$75.31** → $57 ≈ **24.3%** downside.  
Critic is correct: soft-sell ≠ margin of safety until engagement KPIs rebut the Wells thesis. `lens:lynch_buffett`

### AMZN

| Field | Value | Source |
|-------|-------|--------|
| Spot (Fri close) | **$253.51** (+0.92% vs **$251.19** prior) | [Exa AMZN 2026-09-18](https://exa.ai/library/markets/stock/AMZN?date=2026-09-18) |
| Market cap | ~**$2.74T** | [StockAnalysis AMZN stats](https://stockanalysis.com/stocks/amzn/statistics/) (Sep 18/20, 2026 pull; close cited there ~$253.71 — small variance vs Exa) |
| Enterprise value | ~**$2.87T** | StockAnalysis |
| Trailing P/E | ~**20.4×** | StockAnalysis |
| Forward P/E | ~**27.6×** | StockAnalysis |
| EV/Sales | ~**3.69×** | StockAnalysis |
| FCF yield | **−0.42%** (TTM FCF outflow; AI capex) | StockAnalysis; consistent with Amazon IR TTM FCF outflow |

**Read:** AMZN is **not** a distressed soft-sell on Friday’s tape. Value case is “pay a fair multiple for a durable cash machine,” not “catch a falling knife.” Negative TTM FCF yield is the honest cost of AWS/AI build — priced as growth infrastructure, not deep value. `lens:lynch_buffett`

---

## RH basis: UNKNOWN

**Attempted paths (Sun Sep 20, 2026):**
1. **Sherwood docs / skill** — Chain `9994663` is the Tenderly RH mainnet fork; stock tokens (TSLA/AMZN/PLTR/NFLX/AMD) exist on protocol, routing via Uniswap/Synthra-class adapters ([sherwood.sh/skill.md](https://sherwood.sh/skill.md); docs deployments / PortfolioStrategy pages).
2. **Sherwood HTTP API** — `GET https://api.sherwood.sh/chains` returns chain `9994663` (“Robinhood Chain (fork)”) with vault/factory addresses, but **`priceRouter`: `0x000…000`** and **only WETH + WOOD** in the public `tokens` map — **no TSLA/AMZN/PLTR/NFLX/AMD quote endpoints** exposed.
3. **Tenderly public RPC** — `eth_chainId` → `0x9881a7` (= **9994663**) succeeded at the bundled fork RPC from skill.md. No public documented method returned mid-market tokenized equity quotes for the watchlist without token addresses + Quoter calldata (addresses not in public API token map).
4. **app.sherwood.sh** — No public `/api/.../tokens` or markets quote path found (404 HTML).

**Conclusion:** On-chain RH mid, depth, and **basis vs US cash = UNKNOWN** for TSLA/AMZN/PLTR/NFLX/AMD this pass.

**Recommended haircut / sizing gate (Research → PM/Risk):**  
- **Do not size** RH-token exposure as if it equals US cash until a live Synthra/Uniswap quoter mid (or Chainlink stock feed print) is captured and basis vs Exa Fri close is measured.  
- Until then, treat any paper basket as **US-proxy narrative only** and apply an explicit **basis/liquidity haircut** (PM/Risk owns the number; Research suggests a conservative default of **wide — e.g. treat 7d paper PnL as unreliable** if basis unknown).  
- Kill if basis later prints so wide that the cash thesis cannot transfer on-chain.

---

## Primary KPI support (real figures; no fake 10-Q text)

### NFLX — engagement / membership

| KPI | Figure | Primary vs secondary | Source |
|-----|--------|----------------------|--------|
| Paid memberships | **>325M** at YE 2025 (company stopped quarterly sub prints; milestone disclosure) | **Primary** (co. shareholder letter / reputable coverage of co. letter) | [Variety — Q4’25 letter summary](https://variety.com/2026/tv/news/netflix-q4-2025-financial-earnings-subscribers-1236635615/) |
| H1’26 view hours | **>97B hours**, **+2% YoY** (vs +1.5% in 2025); company calls engagement “healthy” despite Olympics/World Cup | **Primary** (Q2’26 shareholder letter / 8-K exhibits) | [StockTitan NFLX 8-K summary](https://www.stocktitan.net/sec-filings/NFLX/8-k-netflix-inc-reports-material-event-c9fc6c407c82.html); [OpenCapital 8-K](https://www.opencapital.sh/filings/0001065280-26-000211); co. letter PDF ([q4cdn FINAL-Q2-26](https://s22.q4cdn.com/959853165/files/doc_financials/2026/q2/FINAL-Q2-26-Shareholder-Letter.pdf)) |
| Q2’26 revenue | **$12.6B**, **+13% YoY** (+12% FXN); op. margin **~33%** | **Primary** | Same 8-K / letter |
| 2026 guide (narrowed) | Revenue **$51.0–$51.4B**; op. margin **31.5%**; ads ~**$3B** | **Primary** | Same |
| Wells engagement thesis | Street-low **$57** Underweight; press summarizes softer engagement / content-slate worry (Cahall) | **Secondary** (analyst note via press — **not** company KPI) | [GuruFocus](https://www.gurufocus.com/news/9088313/netflix-drops-48-as-wells-fargo-cuts-its-target-to-57) |

**Gap flag (honest):** We do **not** have the full Wells Fargo note PDF or a primary Top-100-originals hours series in this brief. Company aggregate view-hours (**+2% H1’26**) and Wells’ bearish engagement framing can **coexist** if Wells is focused on *quality/hit-rate of originals* rather than total hours. **KPI gap remains** until next co. engagement print or a primary note excerpt. Do not invent 10-Q language. `lens:lynch_buffett`

### AMZN — AWS / growth

| KPI | Figure | Primary vs secondary | Source |
|-----|--------|----------------------|--------|
| Q2’26 net sales | **$200.6B**, **+20% YoY** | **Primary** | [Amazon IR — Q2 2026 results](https://ir.aboutamazon.com/news-release/news-release-details/2026/Amazon-com-Announces-Second-Quarter-Results/) |
| Q2’26 operating income | **$27.5B**, **+43% YoY** | **Primary** | Amazon IR |
| **AWS** sales | **$42.2B**, **+37% YoY** (fastest in 18 quarters); ~**$169B** annualized run-rate | **Primary** | Amazon IR |
| AWS operating income | **$16.6B** (vs $10.2B YoY) | **Primary** | Amazon IR |
| AWS backlog | **$496B** (mgmt commentary) | **Primary** (earnings call / IR narrative) | Amazon IR; [Motley Fool transcript](https://www.fool.com/earnings/call-transcripts/2026/08/07/amazon-amzn-q2-2026-earnings-call-transcript/) |
| TTM FCF | Outflow (**−$7.6B** per IR; StockAnalysis ≈ **−$11.6B** depending on definition/timing) | **Primary** / aggregator | Amazon IR; StockAnalysis |
| 2026 cash CapEx guide | Raised to **~$220B** (AI infra) | **Primary** | Amazon IR / CNBC earnings wrap |
| FTC Prime refunds | Refunds up to **$200**; aggregate settlement cap still **$2.5B** | **Secondary** news | [StockTi](https://stockti.com/amazon-expands-prime-refunds-up-to-200-settlement-cap-unchanged) |

**Gap flag:** Next print must confirm AWS growth does not decelerate vs the Q2 re-acceleration story. Capex/FCF tradeoff is already visible in negative FCF yield. `lens:lynch_buffett`

---

## Asymmetry (NFLX) — quantify downside vs upside case

| Scenario | Level | Δ vs Fri spot **$71.78** | Notes |
|----------|-------|--------------------------|-------|
| **Bear / Wells** | **$57** | **≈ −20.6%** | Published Street-low PT; Critic kill geometry |
| Soft bear (halfway to Wells) | ~$64.40 | ≈ −10.3% | If engagement scare half-prices |
| Spot (Fri) | $71.78 | 0% | Post-downgrade print |
| Prior close (pre-gap) | $75.31 | ≈ +4.9% | Mean-revert tape only |
| **Bull case A — prior Wells PT restored** | **$80** | **≈ +11.5%** | If Cahall thesis fails and house re-rates to old PT |
| **Bull case B — GF Value (model, secondary)** | ~$102 | ≈ +42% | GuruFocus GF Value note — **model, not a desk target** |

**Asymmetry read (honest):** Near-term **published** path is skewed: **~20.6% downside to Wells $57** vs **~11.5% upside to the *prior* Wells $80** if the engagement scare fades. The soft-sell is **not** positively asymmetric on the Wells tape alone. Bull needs either (a) primary engagement rebuttal on the next print, or (b) a higher non-Wells consensus path — neither is proven in this brief. 7d paper loop has limited edge proving Street wrong. `lens:lynch_buffett`

**AMZN asymmetry:** No equivalent Street-low PT shock this week. Consensus avg PT ~**$328** (~**+29%** vs ~$254) per StockAnalysis — but that is **crowded AWS narrative upside**, not a value dislocation. Downside is multiple compression if AWS decelerates / CapEx stays elevated (FCF already negative). Not a soft-sell entry. `lens:lynch_buffett`

---

## Thesis bullets (≤8)

1. **NFLX — priced soft-sell with open engagement fight:** Fri **$71.78** after Wells **Underweight / $57**; ~**20.6%** further downside if Wells is right. Fair-price entry only if co. H1 view-hours (**+2%**) prove more relevant than Wells’ hit-rate worry. `lens:lynch_buffett`
2. **NFLX — circle of competence:** Streaming KPIs (price, churn proxies, view hours, ads) are observable without exotic models — prefer that over opaque AI-capex fashion on this desk. `lens:lynch_buffett`
3. **NFLX — multiple not fantasy-cheap:** Trailing P/E ~**22–24×** with FCF yield ~**3.6%** is compressed vs history but **not** deep value; “fair” not “cigar butt.” `lens:lynch_buffett`
4. **AMZN — moat you can narrate:** Retail + fulfillment + **AWS +37%** (primary IR) is durable demand; Fri **$253.51** is quiet tape, not a bargain bin. `lens:lynch_buffett`
5. **AMZN — pay fair for the cash machine:** EV/S ~**3.7×**, trailing P/E ~**20×**, but **negative FCF yield** from AI CapEx — growth-without-fantasy means insisting on AWS delivery, not slides. `lens:lynch_buffett`
6. **AMZN — settlement optics ≠ franchise break:** Prime refunds to **$200** with **$2.5B** cap unchanged (secondary) is process, not open-ended liability — still monitor regulatory tax on trust. `lens:lynch_buffett`
7. **RH venue:** Both names are on the tokenized watchlist; **RH basis UNKNOWN** → do not size until quote/basis known. `lens:lynch_buffett`
8. **Value flavor over chase:** Soft-sell **NFLX** (with asymmetric risk disclosed) + durable **AMZN** beat chasing **AMD** on unconfirmed partner hikes. `lens:lynch_buffett`

---

## What is already priced in (Critic ask)

- **NFLX:** One-bank engagement/content scare and a **~4.7%** Fri gap are **partially** priced; **Wells $57 path is not fully priced** (stock still ~26% above that PT). What’s *not* proven priced-out: sustained engagement deterioration vs co. +2% hours print.  
- **AMZN:** AWS strength and AI CapEx are **widely** in the narrative multiple; Fri bid does **not** price a value dislocation. FTC refund optics look largely contained at the **$2.5B** frame (secondary).

---

## What would change my mind

- **NFLX:** Next engagement / paid-membership / viewing-hours print deteriorates vs H1’26 **or** price trades through **$65** without KPI rebuttal **or** second major house cuts on engagement — then soft-sell → value trap. Conversely, primary Top-100 originals hours stabilizing + ads on track to ~$3B would strengthen the fair-price case. `lens:lynch_buffett`  
- **AMZN:** AWS growth or op. income decelerates vs Q2 run-rate on next print **or** CapEx/FCF path worsens without backlog conversion **or** liability expands beyond **$2.5B** settlement frame. `lens:lynch_buffett`  
- **Both:** Live RH quote showing material adverse basis vs US cash → thesis does not transfer; **do not size**. `lens:lynch_buffett`

---

## Sources cited (real links only; no fabricated filings)

| Item | Link |
|------|------|
| Scan / Critic / Mandate | `workspace/briefs/scan.md`, `workspace/briefs/critic.md`, `workspace/mandate.md` |
| NFLX / AMZN Fri closes | [Exa NFLX](https://exa.ai/library/markets/stock/NFLX?date=2026-09-18), [Exa AMZN](https://exa.ai/library/markets/stock/AMZN?date=2026-09-18) |
| NFLX multiples | [Signals.AI](https://signals.ai/symbol/nflx), GuruFocus valuation notes |
| NFLX Wells downgrade | [GuruFocus](https://www.gurufocus.com/news/9088313/netflix-drops-48-as-wells-fargo-cuts-its-target-to-57), [ECM Source](https://ecmsource.com/netflix-downgraded-underweight-wells-fargo-target-57-september-2026/) |
| NFLX primary KPIs | [StockTitan 8-K](https://www.stocktitan.net/sec-filings/NFLX/8-k-netflix-inc-reports-material-event-c9fc6c407c82.html), [IR shareholder letter PDF](https://s22.q4cdn.com/959853165/files/doc_financials/2026/q2/FINAL-Q2-26-Shareholder-Letter.pdf), [Variety YE’25 subs](https://variety.com/2026/tv/news/netflix-q4-2025-financial-earnings-subscribers-1236635615/) |
| AMZN multiples | [StockAnalysis](https://stockanalysis.com/stocks/amzn/statistics/) |
| AMZN primary KPIs | [Amazon IR Q2 2026](https://ir.aboutamazon.com/news-release/news-release-details/2026/Amazon-com-Announces-Second-Quarter-Results/) |
| AMZN Prime refunds | [StockTi](https://stockti.com/amazon-expands-prime-refunds-up-to-200-settlement-cap-unchanged) |
| RH fork / API | [sherwood.sh/skill.md](https://sherwood.sh/skill.md), `GET https://api.sherwood.sh/chains` (`priceRouter` zero on chain `9994663`) |

**Not claimed:** No invented 10-K/10-Q excerpts; no Buffett/Lynch personal endorsement; no RH on-chain fills or measured basis.

---

## Quality-gate checklist (vs Critic REJECT)

| Critic requirement | Status |
|--------------------|--------|
| Valuation table per candidate (price, mkt cap/EV, multiple) | **Closed** for both (NFLX EV still unavailable — stated honestly) |
| Wells $57 vs spot downside math (~20%+) | **Closed** (~20.6% from $71.78) |
| RH fork basis note | **Closed as UNKNOWN** + haircut / do-not-size until known |
| Primary KPI support | **Closed** with primary IR/letter figures; Wells engagement detail remains **secondary gap** (flagged) |
| Asymmetry quantified | **Closed** (NFLX −20.6% vs +11.5% to prior Wells $80) |
| `lens:lynch_buffett`; 1–3 candidates; no sizing | **Closed** (2 candidates) |

---

## Handoff

- **Critic / PM / Risk:** Own pushback, basket construction, and sizing (≤30% single name, ≤5 names, 7d). Default: treat RH as untradeable-for-size until basis prints.  
- **Research scope ends here** — did not write `critic.md`, `draft.json`, or `risk.md`.
