---
rhBasis: UNKNOWN
liveReady: false
asOf: 2026-09-20T13:05:00-05:00
asOfLabel: America/Bogota (COT)
sessionNote: US cash markets closed (Sunday); movers = Fri 2026-09-18 regular session (Exa Markets / session prints)
venue: Sherwood RH fork chainId 9994663
watchlistEligible: [AAPL, TSLA, NVDA, MSFT, AMZN, AMD, SPY, QQQ, GOOGL]
watchlistExcluded: [META, SLV]
notOnForkRegistry: [PLTR, NFLX]
---

# Scan brief — 2026-09-20 (Sunday, America/Bogota)

## Status / data gaps

- **rhBasis: UNKNOWN** — no live fork mids measured this pass (CLI `fork-tokens.ts` carries stale `quotedOut` / `feedDeviationBps` snapshots only; not treated as live book).
- **liveReady: false** — do not size or submit live PortfolioStrategy until fork quoter mids are captured and basis vs US cash is checked.
- **Universe source:** `@sherwoodagent/cli` `src/lib/fork-tokens.ts` → `ROBINHOOD_FORK_STOCKS` on chain **9994663**.
- **X sentiment:** `user-X--imthatcarlos` `search_posts_all` (Fri–Sun window). `user-X--sherwoodagent` returned client-not-enrolled.

## Exclusions (do not basket)

| Sym | Address | Why |
|-----|---------|-----|
| **META** | `0xc0D6457C16Cc70d6790Dd43521C899C87ce02f35` | Pool ~**8.3%** above frozen META/USD feed (v3 agrees ~+8.2%); past sim 5% buy floor / near 10% ceiling. |
| **SLV** | `0x411eFb0E7f985935DAec3D4C3ebaEa0d0AD7D89f` | **v3-only** on this vnet; no v4 USDG pool; v3 position manager corrupted → swap leg unverified. |
| **PLTR / NFLX** | — | Old testnet five — **not** in fork registry; do not use without proving fork addresses. |

## Top movers — eligible 9 (US cash proxy, Fri 2026-09-18)

Sources: Exa Markets session pages for Fri Sep 18 closes / session Δ. Weekend — no fresh cash prints. Ranked by session strength (proxy).

| Sym | Fri close | Session Δ % (proxy) | Activity read |
|-----|-----------|---------------------|---------------|
| **AMD** | $559.82 | **+2.70%** | Largest upside; AI/supply + Piper OW $600 narrative into Fri |
| **NVDA** | $222.27 | **+1.34%** | Chip leadership; high volume (~190M) |
| **AMZN** | $253.71 | **+1.00%** | Mega-cap support; AWS/Nvidia framing |
| **QQQ** | $721.45 | **+0.63%** | Nasdaq proxy bid; narrow leadership |
| **GOOGL** | $349.54 | **+0.64%** | Modest green; X chart-bulls active into weekend |
| **SPY** | $761.69 | **−0.12%** | Flat broad tape |
| **AAPL** | $336.13 | **−0.26%** | Quiet soft; still high-liquidity core |
| **TSLA** | $364.27 | **−0.53%** | Soft; Cybercab/NHTSA + rates overhang |
| **MSFT** | $493.78 | **−0.80%** | Softest mega-cap Friday; Azure narrative still constructive on X |

**Ranked movers (eligible):** AMD (+2.7%) → NVDA (+1.3%) → AMZN (~+1%) → QQQ/GOOGL (~+0.6%) → SPY flat → AAPL/TSLA soft → MSFT (−0.8%).

## X sentiment (value mandate flavor)

Window ~Thu Sep 17 – Sun Sep 20 (America/Bogota). Not a quantitative score — narrative heat for basket design.

| Theme | Read | Implication for value baskets |
|-------|------|-------------------------------|
| **AI complex bounce** | Posts flag midweek AI selloff then rebound in $NVDA / $AMD; buy-watchlists common | Activity is real; **do not chase** AMD Fri +2.7% as “value” — size as satellite or skip |
| **PEG / value-trap caution** | Semis PEG looks “cheap” called a trap ($NVDA/$AMD cited) | Favors **quality cash engines** (MSFT/AAPL/AMZN/GOOGL) over pure semi momentum |
| **GOOGL setup bullish** | High-engagement chart posts targeting higher; TPU/external HW narrative | Supports GOOGL as **quality AI-adjacent** (not just NVDA chase) |
| **MSFT fundamentals** | Azure + backlog / AI CapEx scale praised despite soft Fri print | Soft tape + strong narrative = **value-flavored** entry candidate vs chase names |
| **Mega rotation** | Weekend rotation posts into $META/$AMZN/$GOOGL/$NVDA (META **excluded** on fork) | Use AMZN/GOOGL/NVDA proxies; **never substitute META** |
| **TSLA** | Thin in this value query set; regulatory/yield noise known | Prefer as optional satellite only |

## Mandate reminder

- PortfolioStrategy only · **value** flavor · max 5 names · max **30%** single (≤3000 bps) · 7d default.
- Forbidden: opaque calldata, memecoins, unlisted venues, META/SLV, PLTR/NFLX unless fork addresses proven.
- Venue = RH fork **9994663** only.

## Blockers

1. Capture **live** fork quoter mids for the 9 eligible vs Fri cash closes → set `rhBasis` and only then consider `liveReady: true`.
2. Cash market closed until Mon open America/New_York — weekend briefs stay on Fri session prints.
3. Owner picks basket from `briefs/basket-options.md` — Scanner does **not** pick a winner.
