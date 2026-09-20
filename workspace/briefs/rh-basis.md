# RH basis — basket 3 (AI Delivery)

**Dated:** Sunday, Sep 20, 2026 (America/Bogota)  
**Chain:** 9994663 (RH fork)  
**Basket:** MSFT / GOOGL / NVDA / AMZN / QQQ  
**RPC:** Tenderly fork public endpoint  
**Method:** Chainlink `latestRoundData` on fork feeds + Yahoo cash refs + CLI `feedDeviationBps` (pool vs feed from `@sherwoodagent/cli` fork-tokens)

## Gate

| Field | Value |
|-------|-------|
| `rhBasis` | **OK** (oracle readable) / **CAUTION** on NVDA+AMZN pool vs feed |
| `liveReady` | **conditional** — owner must accept haircut/slippage on NVDA (~2.0%) and AMZN (~2.6%) pool premium vs feed; MSFT/GOOGL/QQQ fine |
| Prior | UNKNOWN (no quotes) |

Feed timestamps on-fork read ~2026-11-02 (fork clock ≠ wall clock). Treat as fork state, not wall-time freshness.

## Table (basket 3)

| Symbol | US cash (Yahoo) | Fork Chainlink USD | Cash vs feed (bps) | CLI pool vs feed (bps) | Implied pool mid | Pool vs Yahoo (bps) |
|--------|-----------------|--------------------|--------------------|------------------------|------------------|---------------------|
| MSFT | 493.78 | 495.82 | −41 | −18 | ~494.93 | ~+23 |
| GOOGL | 349.54 | 350.47 | −27 | +11 | ~350.86 | ~+38 |
| NVDA | 222.27 | 222.45 | −8 | **+199** | ~226.87 | **~+207** |
| AMZN | 253.71 | 253.86 | −6 | **+264** | ~260.56 | **~+270** |
| QQQ | 721.45 | 720.37 | +15 | +23 | ~722.02 | ~+8 |

## Addresses (fork)

| Symbol | Token | Feed |
|--------|-------|------|
| MSFT | `0xe93237C50D904957Cf27E7B1133b510C669c2e74` | `0x45C3C877C15E6BA2EBB19eA114Ea508d14C1Af2E` |
| GOOGL | `0x2e0847E8910a9732eB3fb1bb4b70a580ADAD4FE3` | `0xF6f373a037c30F0e5010d854385cA89185AE638b` |
| NVDA | `0xd0601CE157Db5bdC3162BbaC2a2C8aF5320D9EEC` | `0x379EC4f7C378F34a1B47E4F3cbeBCbAC3E8E9F15` |
| AMZN | `0x12f190a9F9d7D37a250758b26824B97CE941bF54` | `0xD5a1508ceD74c084eBf3cBe853e2C968fB2a651C` |
| QQQ | `0xD5f3879160bc7c32ebb4dC785F8a4F505888de68` | `0x80901d846d5D7B030F26B480776EE3b29374C2ae` |
| USDG | `0x5fc5360D0400a0Fd4f2af552ADD042D716F1d168` | — |

## Notes

1. Cash↔oracle gap is **small** (<50 bps) for all five — RH fork feeds track US cash well for this basket.
2. **Execution basis** (pool vs feed) is the real risk: NVDA ~2.0% and AMZN ~2.6% above feed (still under META’s ~8% exclusion / ~5% sim buy floor). MSFT/GOOGL/QQQ ≈ flat.
3. Live Uniswap QuoterV2 fill not re-run this pass — CLI `feedDeviationBps` used as pool proxy. Ops should re-quote at propose time.
4. PriceRouter on fork is `0x0` — no protocol mid aggregator; rely on feeds + pool.

## Handoff

- **Risk / Owner:** to flip `liveReady: true`, accept written haircut on NVDA+AMZN pool premium (or trim those weights).
- **Ops:** re-quote pools before any `strategy propose`.
- **Scanner:** replace prior `rhBasis: UNKNOWN` with this file.
