---
rhBasis: OK
liveReady: true
asOf: 2026-09-20T13:13:00-05:00
asOfLabel: America/Bogota (COT)
sessionNote: US cash markets closed (Sunday); movers + cash refs = Fri 2026-09-18; basis = live fork feeds + live v4 pool quotes
venue: Sherwood RH fork chainId 9994663
watchlistEligible: [AAPL, TSLA, NVDA, MSFT, AMZN, AMD, SPY, QQQ, GOOGL]
watchlistExcluded: [META, SLV]
notOnForkRegistry: [PLTR, NFLX]
basket3: [MSFT, GOOGL, NVDA, AMZN, QQQ]
basisDetail: workspace/briefs/rh-basis.md
basisCommit: bf80141d2128531cdb2a28bcd1ff5ae63af7ed2e
---

# Scan brief — 2026-09-20 (Sunday, America/Bogota)

## Status / data gaps

- **rhBasis: OK** — basket 3 live Chainlink feeds + live Uniswap **v4** pool mids on fork `9994663` (see [`rh-basis.md`](./rh-basis.md)). Max |pool vs Fri cash| **53.2 bps** (GOOGL).
- **liveReady: true** — basis gate cleared for paper→live on basket 3. Prior CLI `feedDeviationBps` (NVDA +199 / AMZN +264) are **stale**; live v4 quotes show NVDA/AMZN within ~30 bps of cash.
- **Universe source:** `@sherwoodagent/cli` `fork-tokens.ts` → `ROBINHOOD_FORK_STOCKS` on chain **9994663**.
- Fork chain time ~2026-11-02 (virtual); wall clock Sep 20 — treat feed freshness vs fork tip.

## Exclusions (do not basket)

| Sym | Address | Why |
|-----|---------|-----|
| **META** | `0xc0D6457C16Cc70d6790Dd43521C899C87ce02f35` | Pool ~**8.3%** above frozen META/USD feed (v3 agrees ~+8.2%); past sim 5% buy floor / near 10% ceiling. |
| **SLV** | `0x411eFb0E7f985935DAec3D4C3ebaEa0d0AD7D89f` | **v3-only** on this vnet; no v4 USDG pool; v3 position manager corrupted → swap leg unverified. |
| **PLTR / NFLX** | — | Old testnet five — **not** in fork registry; do not use without proving fork addresses. |

## Top movers — eligible 9 (US cash proxy, Fri 2026-09-18)

Sources: Exa Markets / Yahoo Fri Sep 18 closes. Weekend — no fresh cash prints.

| Sym | Fri close | Session Δ % (proxy) | Activity read |
|-----|-----------|---------------------|---------------|
| **AMD** | $559.82 | **+2.70%** | Largest upside; AI/supply narrative |
| **NVDA** | $222.27 | **+1.34%** | Chip leadership |
| **AMZN** | $253.71 | **+1.00%** | Mega-cap support |
| **QQQ** | $721.45 | **+0.63%** | Nasdaq proxy bid |
| **GOOGL** | $349.54 | **+0.64%** | Modest green |
| **SPY** | $761.69 | **−0.12%** | Flat broad tape |
| **AAPL** | $336.13 | **−0.26%** | Quiet soft |
| **TSLA** | $364.27 | **−0.53%** | Soft; Cybercab/rates overhang |
| **MSFT** | $493.78 | **−0.80%** | Softest mega-cap Friday |

## Basket 3 basis (live)

| symbol | usCash | chainlinkUsd | poolMidIfAny | deviationBps | status |
|--------|--------|--------------|--------------|--------------|--------|
| MSFT | 493.78 | 495.8227 | 495.9211 | +43.4 | OK |
| GOOGL | 349.54 | 350.4714 | 351.3984 | +53.2 | OK |
| NVDA | 222.27 | 222.4473 | 221.6536 | −27.7 | OK |
| AMZN | 253.71 | 253.8630 | 254.4271 | +28.3 | OK |
| QQQ | 721.45 | 720.3651 | 722.5775 | +15.6 | OK |

Full detail: [`rh-basis.md`](./rh-basis.md). Pool mids from V4Quoter (100 USDG); QuoterV2 reverts on these pairs.

## Mandate reminder

- PortfolioStrategy only · **value** flavor · max 5 names · max **30%** single · 7d default.
- Forbidden: opaque calldata, memecoins, unlisted venues, META/SLV, PLTR/NFLX unless fork addresses proven.
- Venue = RH fork **9994663** only. **No weights** (PM).

## Blockers

1. ~~Capture live fork quoter mids~~ — **done** (v4); `rhBasis: OK`, `liveReady: true`.
2. Cash closed until Mon America/New_York open — Mon gap risk remains; re-quote at propose.
3. Owner picks basket from `briefs/basket-options.md` — Scanner does **not** pick a winner.
