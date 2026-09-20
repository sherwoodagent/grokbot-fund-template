---
rhBasis: OK
liveReady: true
asOf: 2026-09-20T13:13:00-05:00
asOfLabel: America/Bogota (COT)
basket: [MSFT, GOOGL, NVDA, AMZN, QQQ]
basketLabel: Grok Fund Beta basket 3 (AI Delivery)
chainId: 9994663
rpc: Tenderly RH fork (moonwell/wormhole-bridge/f509bc-4fdefe)
usCashSource: Exa Markets / Yahoo Fri 2026-09-18 closes (markets closed Sunday)
poolSource: live Uniswap v4 Quoter 0x8dc1…8f94 quoteExactInputSingle 100 USDG → stock (fee 3000 / ts 60 / hooks 0)
feedSource: live eth_call latestRoundData (8 decimals)
---

# RH basis — basket 3 vs US cash

**Overall: `rhBasis: OK`** · **`liveReady: true` can flip** (paper→live) for this basket.

Live fork Chainlink feeds and Uniswap v4 pool quotes were measured on chain **9994663**. All five names are within **~55 bps** of Fri US cash closes. Prior CLI `feedDeviationBps` snapshots (NVDA +199 / AMZN +264) are **stale** — live pool mids do **not** show that premium.

## Gate

| Field | Value |
|-------|-------|
| `rhBasis` | **OK** |
| `liveReady` | **true** (basis gate cleared for basket 3) |
| Max \|pool vs cash\| | **53.2 bps** (GOOGL) |
| Max \|feed vs cash\| | **41.4 bps** (MSFT) |

## Table

| symbol | usCash | chainlinkUsd | poolMidIfAny | deviationBps | status |
|--------|--------|--------------|--------------|--------------|--------|
| MSFT | 493.78 | 495.8227 | 495.9211 | +43.4 | OK |
| GOOGL | 349.54 | 350.4714 | 351.3984 | +53.2 | OK |
| NVDA | 222.27 | 222.4473 | 221.6536 | −27.7 | OK |
| AMZN | 253.71 | 253.8630 | 254.4271 | +28.3 | OK |
| QQQ | 721.45 | 720.3651 | 722.5775 | +15.6 | OK |

`deviationBps` = `(poolMid − usCash) / usCash × 10000` (execution mid vs Fri cash). Threshold used: \|dev\| < 100 → **OK**.

## Method notes

1. **US cash** — Fri 2026-09-18 regular-session closes (Sunday wall clock; no Mon print yet).
2. **Chainlink** — `latestRoundData` on fork feeds; decimals=8; answers decode cleanly. Feed `updatedAt` ≈ fork tip time **2026-11-02 17:46 COT** (vnet virtual clock ≠ wall Sep 20). Treat freshness vs **fork tip**, not wall clock.
3. **Pool mid** — V4Quoter `0x8dc178efb8111bb0973dd9d722ebeff267c98f94`, `quoteExactInputSingle` selling **100 USDG** into each stock pool (`v4:3000:60`, hooks `0x0`). `poolMid ≈ 100 / stockOut`. QuoterV2 (`0x33e8…`) **reverts** (pools are v4-only for these pairs). SwapAdapter `0x54E6A7af53143556973493fDeC9d7837A77c67eF`; StateView cross-check `0xf333…` slot0 mids agree within ~1–2 USD of quote mid.
4. **CLI hints** — `@sherwoodagent/cli` `fork-tokens.ts` `quotedOut` / `feedDeviationBps` left as historical hints only; **not** used for this gate after live re-quote.

## Addresses

| symbol | token | feed |
|--------|-------|------|
| MSFT | `0xe93237C50D904957Cf27E7B1133b510C669c2e74` | `0x45C3C877C15E6BA2EBB19eA114Ea508d14C1Af2E` |
| GOOGL | `0x2e0847E8910a9732eB3fb1bb4b70a580ADAD4FE3` | `0xF6f373a037c30F0e5010d854385cA89185AE638b` |
| NVDA | `0xd0601CE157Db5bdC3162BbaC2a2C8aF5320D9EEC` | `0x379EC4f7C378F34a1B47E4F3cbeBCbAC3E8E9F15` |
| AMZN | `0x12f190a9F9d7D37a250758b26824B97CE941bF54` | `0xD5a1508ceD74c084eBf3cBe853e2C968fB2a651C` |
| QQQ | `0xD5f3879160bc7c32ebb4dC785F8a4F505888de68` | `0x80901d846d5D7B030F26B480776EE3b29374C2ae` |
| USDG | `0x5fc5360D0400a0Fd4f2af552ADD042D716F1d168` (6 dec) | — |

## Residual caveats (do not block OK)

- Cash ref is **Fri close**; Mon open can gap. Re-check basis at propose if tape moves.
- Fork virtual time (Nov 2 tip) ≠ wall Sep 20 — oracle “age vs wall” is not meaningful; feeds are live relative to fork tip.
- Re-quote at `strategy propose` time (size-aware impact may differ from 100 USDG probe).

## Handoff

- **Scanner / PM:** set `rhBasis: OK`, `liveReady: true` for basket 3 paper→live.
- **Ops:** still re-quote at propose; do not rely on frozen CLI `feedDeviationBps`.
