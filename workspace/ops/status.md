# Ops status

- **lifecycle:** `proposed` (Pending vote)
- **updated:** 2026-09-20 13:30 America/Bogota (UTC-5)
- **chain:** 9994663 (RH fork — test capital only)

## Fund
- vault: `0xE075cc9e3F55007B6D7e63439AA4c8094B84288F` (Grok Fund Beta / `grok-fund-beta`)
- agent (Privy): `0x3E11F357De42Ae396fCA812db5f8FA1C576DC225`
- owner: same as agent
- totalAssets: 500 USDG
- rpc: Tenderly fork endpoint (moonwell/wormhole-bridge/f509bc-4fdefe)

## Live propose — AI Delivery anchored
| Field | Value |
|-------|-------|
| **proposal id** | **1** |
| state | Pending |
| name | AI Delivery anchored |
| strategy | PortfolioStrategy clone `0xC8C43CA731f007c4dE3FD3e5c969D07c9Fabc56E` |
| amount | 500 USDG |
| tokens / weights | MSFT 2800 / GOOGL 2500 / NVDA 2000 / AMZN 1500 / QQQ 1200 |
| routes | v4:3000:60 (all legs) |
| duration | 7d |
| proposer bond | 2250.367780758290425953 WOOD locked in ProposerBondEscrow |
| WOOD remaining | ~2749.63 (15k faucet − 10k owner stake − bond) |
| metadata | see on-chain proposal |

## Gates
| Gate | Status |
|------|--------|
| Privy wallet | PASS |
| fund.json vault+agent | PASS |
| Risk APPROVE | PASS (live-candidate + owner propose-now) |
| RH basis / liveReady | PASS — re-quote max \|dev\| ~53 bps GOOGL |
| live propose | **DONE** → proposal #1 Pending |

## Tx log
| Step | Hash | Block | Status |
|------|------|-------|--------|
| cloneAndInitDeterministic | `0xd255237db0d132cc95a0a3526b9223115bfd043b07ab5ddb896deb2d60f445dc` | 67081705 | success |
| WOOD approve → ProposerBondEscrow (5k) | `0x7706419a3bbedaa8cb88d3ee944fff23852c7c06aa96e9a7662f3f3e7b131995` | 67081707 | success |
| governor.propose (#1) | `0x5f8f61f9bdce4abe98bd51b6df6e886228766716dd71034ffafdf56783dabbf0` | 67081713 | success |

Explorer: https://dashboard.tenderly.co/explorer/vnet/6ad5961e-fbca-452f-939f-ca9a8c020933

## Next
Voting window (~1d fork time). Then guardian review → execute → settle. Do not open a second proposal (`VaultHasOpenProposal`).
