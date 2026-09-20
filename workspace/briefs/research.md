# Research brief — Basket 3 (owner locked): AI Delivery

```
ownerPick: Option 3 — AI Delivery (anchored, not chase)
basket: [MSFT, GOOGL, NVDA, AMZN, QQQ]
ownerWeightsBps: MSFT 2800 / GOOGL 2500 / NVDA 2000 / AMZN 1500 / QQQ 1200  # sum 10000; OWNER locked — Research does not re-pick
sizing: LEFT_TO_PM  # honor owner weights within mandate (≤3000 bps single); Research does not size
rhBasis: OK  # live V4 quotes; max |pool−cash| ~53 bps — see workspace/briefs/rh-basis.md
rhBasisCommits: bf80141 / fedcf68
liveReady: basis_gate_cleared  # Risk re-gates live after this depth pass
asOf: 2026-09-20T13:17:00-05:00
asOfLabel: Sunday Sep 20, 2026 America/Bogota (COT / UTC-5)
cashSession: 2026-09-18 (Fri close; weekend — markets closed Sunday)
venue: Sherwood RH fork chain 9994663
mandate: PortfolioStrategy only · value · max 5 names · ≤3000 bps single · 7d
persona: lynch_buffett (checklist tags only — not Buffett/Lynch endorsement)
duration: 7d
```

**This brief replaces the prior widen-pass options-menu stub on main.** Owner has locked Basket 3 (Option 3). Research does **not** re-pick weights or size.

**RH basis:** **`rhBasis: OK`** — live Uniswap v4 Quoter + Chainlink feeds vs Fri US cash; max |pool−cash| **~53 bps** (GOOGL +53.2). Cite [`workspace/briefs/rh-basis.md`](https://github.com/sherwoodagent/grokbot-fund-template/blob/main/workspace/briefs/rh-basis.md) commits [bf80141](https://github.com/sherwoodagent/grokbot-fund-template/commit/bf80141d2128531cdb2a28bcd1ff5ae63af7ed2e) / [fedcf68](https://github.com/sherwoodagent/grokbot-fund-template/commit/fedcf68da2019669dc5337d0aaf406f78fce5915). Re-quote at propose; Mon gap risk remains.

**Depth kill target (Critic/Risk):** Prior live REJECT cited thin Research on MSFT / GOOGL / NVDA / QQQ. This pass supplies one-line thesis, valuation table, primary KPIs with real sources, and falsifiers for **all five** names.

---

## One-line thesis per name (`lens:lynch_buffett`)

| Symbol | Owner bps | One-line thesis |
|--------|-----------|-----------------|
| **MSFT** | 2800 | Soft Fri print into an understandable Azure/Copilot cash engine — fair-price entry, not a chase. |
| **GOOGL** | 2500 | Cheapest mega trailing multiple in the five with accelerating Cloud — quality at a relative discount. |
| **NVDA** | 2000 | World-class Data Center delivery, **capped** as AI-infra satellite — own the complex without AMD Fri-spike monopoly. |
| **AMZN** | 1500 | Narratable retail + AWS moat at a fair multiple; AWS re-acceleration supports pay-fair, not distressed. |
| **QQQ** | 1200 | Nasdaq-100 ballast for a 7d loop / narrow tape — not a sixth single-name thesis. |

**Sizing:** Owner already picked the weights above. **Leave final bps / propose sizing to PM** within mandate. Research does not recommend alternate weights.

---

## Basket valuation snapshot (US cash — Fri 2026-09-18)

| Symbol | Price | Mkt cap / AUM | One multiple | Source |
|--------|-------|---------------|--------------|--------|
| **MSFT** | **$493.78** (−0.80%) | $3.67T | Trailing P/E **27.51** | [Exa](https://exa.ai/library/markets/stock/MSFT?date=2026-09-18); [StockAnalysis](https://stockanalysis.com/stocks/msft/statistics/) |
| **GOOGL** | **$349.54** (+0.64%) | $4.27T | Trailing P/E **17.54** | [Exa](https://exa.ai/library/markets/stock/GOOGL?date=2026-09-18); [StockAnalysis](https://stockanalysis.com/stocks/googl/statistics/) |
| **NVDA** | **$222.27** (+1.34%) | $5.37T | Trailing P/E **28.11** | [Exa](https://exa.ai/library/markets/stock/NVDA?date=2026-09-18); [StockAnalysis](https://stockanalysis.com/stocks/nvda/statistics/) |
| **AMZN** | **$253.71** (+1.00%) | $2.74T | Trailing P/E **20.40** | [Exa](https://exa.ai/library/markets/stock/AMZN?date=2026-09-18); [StockAnalysis](https://stockanalysis.com/stocks/amzn/statistics/) |
| **QQQ** | **$721.45** (+0.63%) | AUM **$484.28B** | ETF P/E **30.06** | [Exa](https://exa.ai/library/markets/stock/QQQ?date=2026-09-18); [StockAnalysis ETF](https://stockanalysis.com/etf/qqq/) |

Multiples as-of StockAnalysis / S&P GMI pull ~Sep 18–20, 2026. Honest gap: weekend — no Mon open yet; cash ref is Fri close.

---

## MSFT — valuation + KPIs

| Field | Value | Source |
|-------|-------|--------|
| Spot / mkt cap / EV | $493.78 / $3.67T / $3.72T | StockAnalysis (close Sep 18) |
| Trailing / forward P/E | 27.51 / 24.99 | StockAnalysis |
| FCF yield | ~1.83% (TTM FCF ~$67.0B) | StockAnalysis |

**Primary KPIs (FY26 Q4 ended Jun 30, 2026):**
- Microsoft Cloud revenue **$59.3B**, **+27% YoY**
- Azure and other cloud services revenue **+43% YoY**
- Intelligent Cloud revenue **$39.3B**, **+32% YoY**
- Commercial RPO **$678B**, **+84% YoY**

Source: [Microsoft Investor Relations — FY26 Q4 press release](https://www.microsoft.com/en-us/Investor/earnings/FY-2026-Q4/press-release-webcast) (verified Jul 29, 2026 release text).

**What would change my mind:** Azure growth decelerates sharply vs +43% run-rate on next print; commercial RPO growth collapses; CapEx/FCF path worsens without backlog conversion. `lens:lynch_buffett`

---

## GOOGL — valuation + KPIs

| Field | Value | Source |
|-------|-------|--------|
| Spot / mkt cap / EV | $349.54 / $4.27T / $4.15T | StockAnalysis |
| Trailing / forward P/E | 17.54 / 26.11 | StockAnalysis |
| FCF yield | ~1.25% (TTM FCF ~$53.3B) | StockAnalysis |

**Primary KPIs (Q2 2026 ended Jun 30, 2026):**
- Consolidated revenue **$119.8B**, **+24% YoY** (+23% FXN)
- Google Cloud revenue **$24.8B**, **+82% YoY**
- Google advertising revenue **$81.6B** (Search & other + YouTube + Network)
- Operating margin **34%** (op. income +30% YoY)

Source: [Alphabet Q2 2026 8-K Ex. 99.1](https://www.sec.gov/Archives/edgar/data/1652044/000165204426000066/googexhibit991q22026.htm) (SEC EDGAR; verified).

**Honest gap:** Trailing P/E is depressed in part by large Q2 unrealized equity gains in OI&E — use Cloud/ads operating KPIs, not headline EPS alone, for the 7d thesis.

**What would change my mind:** Cloud growth re-decelerates; Search monetization breaks; regulatory fine/remedy materially impairs the cash engine. `lens:lynch_buffett`

---

## NVDA — valuation + KPIs

| Field | Value | Source |
|-------|-------|--------|
| Spot / mkt cap / EV | $222.27 / $5.37T / $5.34T | StockAnalysis |
| Trailing / forward P/E | 28.11 / 18.45 | StockAnalysis |
| FCF yield | ~2.37% (TTM FCF ~$127.0B) | StockAnalysis |

**Primary KPIs (Q2 FY2027 ended Jul 26, 2026):**
- Revenue **$96.2B**, **+106% YoY**, **+18% QoQ**
- Data Center revenue **$89.0B**, **+117% YoY**
- GAAP gross margin **75.0%**; diluted EPS **$2.46**
- Q3 FY27 revenue guide **$108.0B** ±2% (no China Data Center compute assumed)

Sources: [NVIDIA IR — Q2 FY2027 results](https://investor.nvidia.com/news/press-release-details/2026/NVIDIA-Announces-Financial-Results-for-Second-Quarter-Fiscal-2027/default.aspx); [SEC q2fy27pr.htm](https://www.sec.gov/Archives/edgar/data/1045810/000104581026000073/q2fy27pr.htm) (verified Aug 26, 2026 figures).

**What would change my mind:** Data Center guide cut; gross margin cracks >200 bps; hyperscaler CapEx pause; China/export shock beyond already-excluded guide. `lens:lynch_buffett`

---

## AMZN — valuation + KPIs

| Field | Value | Source |
|-------|-------|--------|
| Spot / mkt cap / EV | $253.71 / $2.74T / $2.87T | StockAnalysis |
| Trailing / forward P/E | 20.40 / 27.57 | StockAnalysis |
| FCF yield | **−0.42%** (TTM FCF −$11.6B; AI CapEx) | StockAnalysis |

**Primary KPIs (Q2 2026 ended Jun 30, 2026):**
- Net sales **$200.6B**, **+20% YoY**
- Operating income **$27.5B**, **+43% YoY**
- AWS sales **$42.2B**, **+37% YoY** (~$169B ARR); AWS op. income **$16.6B**

Sources: [Amazon IR — Q2 2026 results](https://ir.aboutamazon.com/news-release/news-release-details/2026/Amazon-com-Announces-Second-Quarter-Results/); [SEC Ex. 99.1](https://www.sec.gov/Archives/edgar/data/1018724/000101872426000024/amzn-20260630xex991.htm) (verified Jul 30, 2026 release).

**Honest gap:** Negative TTM FCF is real AI-infra cost — insist on AWS delivery, do not treat as a free-cash bargain.

**What would change my mind:** AWS decelerates vs +37% re-acceleration; CapEx/FCF path worsens without backlog conversion; retail margin shock. `lens:lynch_buffett`

---

## QQQ — ballast sleeve

| Field | Value | Source |
|-------|-------|--------|
| Fri close | **$721.45** (+0.63%) | [Exa QQQ 2026-09-18](https://exa.ai/library/markets/stock/QQQ?date=2026-09-18) |
| Prior close | $716.92 | StockAnalysis ETF |
| AUM / ETF P/E | **$484.28B** / **30.06** | [StockAnalysis QQQ](https://stockanalysis.com/etf/qqq/) |
| Owner weight | **1200 bps** | basket-options.md Option 3 |

**Primary “KPIs” (ETF — no 10-Q):**
- Tracks Nasdaq-100; expense ratio **0.18%**
- Top holdings overlap basket (NVDA / MSFT / AMZN / GOOGL among largest) — intentional ballast, not a sixth thesis
- Fri tape: QQQ green / SPY soft → prefer QQQ sleeve for growth-vs-broad split in this 7d loop

**What would change my mind:** Owner/Risk rejects ETF sleeves on fork; QQQ fork route/liquidity fails; concentration risk if single-name + QQQ double-counts beyond Risk limit. `lens:lynch_buffett`

---

## Shared kill / what changes the basket mind

- Any name’s next print breaks the KPI path above (Azure / Cloud / AWS / Data Center).
- Live RH mid vs cash basis prints adverse beyond Risk tolerance after re-quote (prior OK does not freeze fills).
- Fork route/quote failure on any of the five.
- NVDA (or complex) gaps violently through Risk limits on Mon open.

---

## Sources (real links only — no fake 10-Q text)

| Item | Link |
|------|------|
| RH basis (`rhBasis: OK`) | `workspace/briefs/rh-basis.md` · [bf80141](https://github.com/sherwoodagent/grokbot-fund-template/commit/bf80141d2128531cdb2a28bcd1ff5ae63af7ed2e) · [fedcf68](https://github.com/sherwoodagent/grokbot-fund-template/commit/fedcf68da2019669dc5337d0aaf406f78fce5915) |
| Owner weights (Option 3) | `workspace/briefs/basket-options.md` |
| Fri closes | [MSFT](https://exa.ai/library/markets/stock/MSFT?date=2026-09-18) · [GOOGL](https://exa.ai/library/markets/stock/GOOGL?date=2026-09-18) · [NVDA](https://exa.ai/library/markets/stock/NVDA?date=2026-09-18) · [AMZN](https://exa.ai/library/markets/stock/AMZN?date=2026-09-18) · [QQQ](https://exa.ai/library/markets/stock/QQQ?date=2026-09-18) |
| Multiples | [MSFT](https://stockanalysis.com/stocks/msft/statistics/) · [GOOGL](https://stockanalysis.com/stocks/googl/statistics/) · [NVDA](https://stockanalysis.com/stocks/nvda/statistics/) · [AMZN](https://stockanalysis.com/stocks/amzn/statistics/) · [QQQ](https://stockanalysis.com/etf/qqq/) |
| MSFT KPIs | [Microsoft FY26 Q4 IR](https://www.microsoft.com/en-us/Investor/earnings/FY-2026-Q4/press-release-webcast) |
| GOOGL KPIs | [SEC Ex. 99.1 Q2’26](https://www.sec.gov/Archives/edgar/data/1652044/000165204426000066/googexhibit991q22026.htm) |
| NVDA KPIs | [NVIDIA Q2 FY27 IR](https://investor.nvidia.com/news/press-release-details/2026/NVIDIA-Announces-Financial-Results-for-Second-Quarter-Fiscal-2027/default.aspx) · [SEC PR](https://www.sec.gov/Archives/edgar/data/1045810/000104581026000073/q2fy27pr.htm) |
| AMZN KPIs | [Amazon IR Q2’26](https://ir.aboutamazon.com/news-release/news-release-details/2026/Amazon-com-Announces-Second-Quarter-Results/) · [SEC Ex. 99.1](https://www.sec.gov/Archives/edgar/data/1018724/000101872426000024/amzn-20260630xex991.htm) |

**Not claimed:** No invented 10-K/10-Q excerpts; no Buffett/Lynch personal endorsement; no new weight recommendations; no measured RH fills beyond `rh-basis.md` / bf80141 / fedcf68.

---

## Handoff

- **Critic / Risk:** Research depth for Basket 3 (MSFT / GOOGL / NVDA / AMZN / QQQ) is **done** — intended to clear the live-depth kill. Risk re-gates live.
- **PM:** Owns final bps within owner-locked Option 3 / mandate. Research does not size.
- **Ops:** Idle until live APPROVE + owner “propose now”; re-quote at propose.
