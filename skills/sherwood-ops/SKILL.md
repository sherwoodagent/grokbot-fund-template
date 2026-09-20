---
name: sherwood-ops
description: Use when minting an ERC-8004 identity, staking WOOD, creating a Sherwood vault, or proposing/executing/settling a PortfolioStrategy on the RH fork with Privy (sign then raw broadcast).
---

# Sherwood Ops (beta)

Canonical detail: `docs/SETUP.md`. Chain id incentivized beta: `9994663` (confirm vs current Sherwood skill).

## Privy CLI

```bash
P="pnpm --package=@privy-io/agent-wallet-cli dlx privy-agent-wallet"
# pnpm dlx — not npx
$P login
$P list-wallets
```

## Sign + broadcast (custom chain)

Always: Privy **sign** (`eth_signTransaction` / typed data) → `eth_sendRawTransaction` to fork RPC.  
Never rely on Privy `eth_sendTransaction` for chain `9994663`.

## Faucet

`POST https://app.sherwood.sh/api/v1/faucet` body `{"address":"0x…"}`  
→ 1 ETH + 15k WOOD + 1k USDG; 1 / address / IP / 24h.

## Identity mint (ERC-8004, Robinhood mainnet — no local private key)

Optional on the fork beta (`create` accepts `--agent-id 0`), **required on production**, highly recommended. Lives on the coordination chain **4663**, not the fork. Needs a dust of **real** mainnet ETH on the Privy address (faucet does not cover it).

1. `sherwood --calldata-only identity mint --name "<Fund> Desk"` or `GET https://api.sherwood.sh/prepare/identity-mint?chainId=9994663&name=…` → one tx to `0x8004A169FB4a3325136EB29fA0ceB6D2e539a432`, `chainId: 4663`. Check `txs[0].chainId` before signing.
2. Privy `eth_signTransaction` (`chain_id: 4663`, mainnet nonce/gas) → `eth_sendRawTransaction` to `https://rpc.mainnet.chain.robinhood.com`.
3. Token id = receipt ERC-721 `Transfer` log `topics[3]`. Verify `balanceOf(agent) == 1`.
4. Write `agentId` to `workspace/fund.json`; pass `--agent-id <agentId>` at create. Skipped → `0`.

## Owner stake + vault create (no local private key)

Desk friction: `sherwood --calldata-only guardian prepare-owner-stake` still wanted a local key for allowance. Do **not** ask for one.

Recipe:
1. Build calldata-only steps via Sherwood CLI/skill: WOOD approve → `prepareOwnerStake` on sWOOD → `vault create --agent-id <agentId>` (USDG asset, open-deposits/public-chat per owner confirm; `agentId` from `fund.json`, `0` if identity skipped).
2. For each tx: Privy sign → raw broadcast.
3. `vault add` — register agent wallet.
4. Optional USDG dust deposit (`totalAssets > 0`).
5. Write `workspace/fund.json` **after** vault address is known.

Get **explicit yes** on name/subdomain/description/flags before any gas.

## Strategy lifecycle

propose → depositors vote (optimistic) → guardian review → execute → settle → cooldown  

PortfolioStrategy only. Pre-commit execute + settle. One live strategy at a time.

## Live gates

Ops only if: Risk APPROVE, `fund.json` has real vault, RH basis known or haircut accepted, Privy sign→raw discipline.
