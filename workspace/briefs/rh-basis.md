# RH basis — morning 2026-09-21T09:22:10-05:00

**Chain:** 9994663 · block `67082393` · fork tip `2026-11-09T04:19:51+00:00`  
**Method:** Uniswap v4 Quoter `0x8dc178efb8111bb0973dd9d722ebeff267c98f94` `quoteExactInputSingle` — sell **100 USDG** → stock (`fee/tick` per watchlist; hooks `0x0`); `poolMid = 100 / stockOut`. Chainlink `latestRoundData` where feed known. US cash = Yahoo live (Mon session).  
**Gate:** `deviationBps = (poolMid − usCash) / usCash × 10000`; `|dev| < 100` → OK else STALE. CLI snapshots **not** used.

## Gate

| Field | Value |
|-------|-------|
| **rhBasis** | **STALE** |
| **liveReady** | **false** |
| Eligible quoted | 9 / 9 |
| STALE names | AMD, QQQ, GOOGL |
| Basket 3 note | Proposal #1 already **Executed** — material STALE now: **GOOGL, QQQ** |

## Table (eligible)

| symbol | usCash | chainlinkUsd | poolMid | deviationBps | status |
|--------|--------|--------------|---------|--------------|--------|
| AAPL | 337.13 | — | 337.55 | 12.6 | **OK** |
| TSLA | 375.70 | — | 376.60 | 24.0 | **OK** |
| NVDA | 223.15 | 223.74 | 221.63 | -67.9 | **OK** |
| MSFT | 494.20 | 494.81 | 495.94 | 35.1 | **OK** |
| AMZN | 256.59 | 256.81 | 254.76 | -71.5 | **OK** |
| AMD | 607.76 | — | 620.81 | 214.6 | **STALE** |
| SPY | 768.30 | — | 769.56 | 16.4 | **OK** |
| QQQ | 733.59 | 730.64 | 723.47 | -138.0 | **STALE** |
| GOOGL | 356.24 | 354.89 | 351.41 | -135.5 | **STALE** |

## Pool keys

| symbol | token | fee | tickSpacing |
|--------|-------|-----|-------------|
| AAPL | `0xaF3D…93f9` | 3000 | 60 |
| TSLA | `0x322F…b2d` | 3000 | 60 |
| NVDA | `0xd060…9EEC` | 3000 | 60 |
| MSFT | `0xe932…2e74` | 3000 | 60 |
| AMZN | `0x12f1…1F54` | 3000 | 60 |
| AMD | `0x8692…fdC` | 10000 | 200 |
| SPY | `0x117c…4C0C` | 3000 | 60 |
| QQQ | `0xD5f3…de68` | 3000 | 60 |
| GOOGL | `0x2e08…4FE3` | 3000 | 60 |

## Feeds (known)

| symbol | feed |
|--------|------|
| MSFT | `0x45C3C877C15E6BA2EBB19eA114Ea508d14C1Af2E` |
| GOOGL | `0xF6f373a037c30F0e5010d854385cA89185AE638b` |
| NVDA | `0x379EC4f7C378F34a1B47E4F3cbeBCbAC3E8E9F15` |
| AMZN | `0xD5a1508ceD74c084eBf3cBe853e2C968fB2a651C` |
| QQQ | `0x80901d846d5D7B030F26B480776EE3b29374C2ae` |
| AAPL / TSLA / AMD / SPY | — not resolved this pass (pool gate still applied) |

## Notes

1. Fork tip clock ≈ **2026-11-09** (virtual); wall ≈ Mon 2026-09-21 America/Bogota. Pool quotes are live eth_call vs tip.
2. Chainlink feed age vs fork tip is large (~48d) for known feeds — **pool vs cash is the gate**, feeds are cross-check only.
3. **AMD +215 bps** pool premium into a +8.5% cash melt-up / $1T narrative — STALE, do not chase for new size.
4. **QQQ −138 / GOOGL −136** pool below cash — STALE; Basket 3 holds these — Risk/Ops should re-quote before any add.
5. Excluded META/SLV unchanged (not quoted).

## Handoff

- New baskets: `liveReady: false` until Risk haircuts STALE names or drops them.
- Basket 3 (executed): flag GOOGL+QQQ STALE to Risk; no Scanner re-propose.
