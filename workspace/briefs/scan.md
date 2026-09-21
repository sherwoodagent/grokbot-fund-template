---
rhBasis: STALE
liveReady: false
asOf: 2026-09-21T09:22:10-05:00
asOfLabel: America/Bogota (COT)
session: US regular Mon 2026-09-21 open
socialSource: X
watchlistEligible: [AAPL, TSLA, NVDA, MSFT, AMZN, AMD, SPY, QQQ, GOOGL]
watchlistExcluded: [META, SLV]
basket3: [MSFT, GOOGL, NVDA, AMZN, QQQ]
basket3Status: Executed (proposal #1) — context only
basisDetail: workspace/briefs/rh-basis.md
---

# Scan brief — Mon 2026-09-21 US open (America/Bogota)

## Status

- **rhBasis: STALE** — live v4 Quoter mids for all 9 eligible. STALE: **AMD (+215 bps), QQQ (−138), GOOGL (−136)**. OK: AAPL/TSLA/NVDA/MSFT/AMZN/SPY.
- **liveReady: false** — fail closed for **new** size until Risk accepts haircuts / drops STALE names.
- **Basket 3 (Executed):** MSFT/NVDA/AMZN still OK; **GOOGL + QQQ now STALE** vs live cash — material for Risk, not a re-propose.
- **socialSource: X** (connected). Detail: [`rh-basis.md`](./rh-basis.md).

## Top movers (Yahoo live vs prior close)

| Rank | Sym | Live | Session % | Note |
|------|-----|------|-----------|------|
| 1 | **AMD** | $607.76 | **+8.56%** | $1T mcap narrative; pool STALE |
| 2 | **TSLA** | $375.70 | **+3.14%** | Strong open; basis OK |
| 3 | **GOOGL** | $356.24 | **+1.92%** | Bid; pool STALE (−136 bps) |
| 4 | **QQQ** | $733.59 | **+1.68%** | Nasdaq bid; pool STALE (−138) |
| 5 | **AMZN** | $256.59 | **+1.14%** | Mega-cap; basis OK |
| 6 | **SPY** | $768.30 | **+0.87%** | Broad green; OK |
| 7 | **NVDA** | $223.15 | **+0.39%** | Quiet vs AMD; OK |
| 8 | **AAPL** | $337.13 | **+0.30%** | Quiet; OK |
| 9 | **MSFT** | $494.20 | **+0.09%** | Flat; OK |

## Narrative spikes

- **AMD $1T / >$600:** Wide X heat at open — mcap milestone + ~+8–10% tape.  
  https://x.com/NxtGen_Trader/status/2102040286830457096 · https://x.com/StockMKTNewz/status/2102030195897032959  
  **Desk:** activity is real; **value mandate → do not chase**; pool already STALE (+215 bps).
- **Semis / QQQ:** “Semiconductors are the market” tape with $SMH/$QQQ/$SPY.  
  https://x.com/pnani456/status/2102040338500092224
- **NVDA / CPU-AI:** Agent/CPU demand narrative alongside GPUs ($AMD/$ARM/$INTC framed).  
  https://x.com/AuroraStecher/status/2102040303016587731
- **TSLA / SpaceX:** Morgan Stanley “deepening alliance” chatter (secondary).  
  https://x.com/TheSonOfWalkley/status/2102040149152641116
- **GOOGL:** Technical/breakout posts near ~$350 zone — cash bid, pool lagging.  
  https://x.com/Levi2v2y/status/2102040190629888155

## Names to ignore today

- **META / SLV** — excluded (feed/pool / v3-only).
- **AMD for new value size** — momentum melt-up + STALE pool; satellite only if Risk haircuts.
- **PLTR / NFLX** — not on fork registry.

## Mandate reminder

PortfolioStrategy · value · max 5 · 30% · 7d · RH fork 9994663. **No weights** (PM). Forbidden: opaque calldata, memecoins, unlisted venues.

## Blockers

1. Risk: haircut or drop **AMD / QQQ / GOOGL** before any new propose.
2. Basket 3 executed book: re-quote GOOGL+QQQ before add/rotate.
3. Ops: re-run v4 quoter at propose size (100 USDG probe ≠ fill size).
