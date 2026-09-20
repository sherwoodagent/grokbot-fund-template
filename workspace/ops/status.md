# Ops status

- **lifecycle:** `executed` (on-chain state: **Executed**)
- **updated:** 2026-09-20 14:18:02 America/Bogota (UTC-5)
- **chain:** 9994663 (RH fork — test capital only)
- **watch:** proposal #1 executed; strategy open; settle after duration

## Fund
- vault: `0xE075cc9e3F55007B6D7e63439AA4c8094B84288F` (Grok Fund Beta / `grok-fund-beta`)
- agent (Privy): `0x3E11F357De42Ae396fCA812db5f8FA1C576DC225`
- totalAssets (pre-execute snapshot): 500 USDG

## Proposal #1
| Field | Value |
|-------|-------|
| name | AI Delivery anchored |
| state | **Executed** |
| clone | `0xC8C43CA731f007c4dE3FD3e5c969D07c9Fabc56E` |
| against | 0 |
| votableSupply | 500e12 (shares) |
| vetoThreshold | 20% |
| snapshot (fork clock) | 2026-11-03 16:44:48 |
| voteEnd (fork clock) | 2026-11-04 16:44:49 |
| executeBy (fork clock) | 2026-11-06 16:44:49 |
| executedAt (fork clock) | 2026-11-05 22:03:20 |
| capital snapshot | $500.00 |
| envelopeTier | 2 |
| maxCapital | 500 USDG |
| proposerBondWood | ~2250.37 WOOD |

## Tx log
| # | step | hash | block | status |
|---|------|------|-------|--------|
| 1 | cloneAndInit | `0xd255237db0d132cc95a0a3526b9223115bfd043b07ab5ddb896deb2d60f445dc` | 67081705 | 0x1 |
| 2 | governor.propose | `0x5f8f61f9bdce4abe98bd51b6df6e886228766716dd71034ffafdf56783dabbf0` | 67081713 | 0x1 |
| 3 | **governor.execute** | `0x95dabc15b22fc421cd297a2d984d0ebeef1834d22baf730bc6799e1d918bc1df` | 67082018 | **0x1** |

Explorer: https://dashboard.tenderly.co/explorer/vnet/6ad5961e-fbca-452f-939f-ca9a8c020933

## Transitions log
| when (COT) | from → to | note |
|------------|-----------|------|
| 2026-09-20 13:25 | none → proposed | propose txs confirmed |
| 2026-09-20 13:26:58 America/Bogota (UTC-5) | proposed → voting/Pending | Desk Lead watch armed; against=0 |
| 2026-09-20 13:52:21 America/Bogota (UTC-5) | Pending → GuardianReview | vote ended; against=0 |
| 2026-09-20 14:15:25 America/Bogota (UTC-5) | GuardianReview → Approved | guardian cleared; execute window open |
| 2026-09-20 14:18:02 America/Bogota (UTC-5) | Approved → **Executed** | execute tx `0x95dabc15…bc1df` confirmed; strategy opened |

## Next
Strategy open for ~7d duration. Monitor positions. **Settle** after duration ends (do not settle early). No second proposal while strategy is live.
