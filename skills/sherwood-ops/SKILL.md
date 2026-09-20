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

`Pending (voting, optimistic) → GuardianReview → Approved → Executed → Settled` (+ `Rejected` / `Cancelled`). PortfolioStrategy only. Pre-commit execute + settle. One live strategy at a time.

## Propose — one-shot recipe (Privy, no local key)

Verified 2026-09-20 on fork `9994663` (proposal #1: cloneAndInit → governor.propose, both `0x1`).

1. **Re-quote basis** for the draft basket at propose size (v4 quoter, see `agents/scanner.md`). Any name `STALE` → stop, back to Risk.
2. **Calldata:**
   ```bash
   sherwood --calldata-only strategy propose portfolio \
     --vault <fund.json vault> --proposer <fund.json agent> \
     --amount <USDG> --asset USDG \
     --tokens MSFT,GOOGL,NVDA,AMZN,QQQ --weights 2500,2000,2000,2000,1500 \
     --name "<draft name>" --description "<draft rationale>" --duration 7d
   ```
   Emits `txs` in order: `StrategyFactory.cloneAndInitDeterministic` (predicted `clone` + `salt`), then `governor.propose`. `propose` pulls the risk-scaled **proposer bond** in WOOD via `ProposerBondEscrow.lockBond` → `transferFrom` — if the CLI lists a WOOD `approve` tx first, it goes first. Quote the bond with `ExposureLedger.proposerBondWood` if you need the number for `ops/status.md`.
3. **Per tx, in order:** Privy `eth_signTransaction` (`chain_id: 9994663`, nonce from `eth_getTransactionCount`, gas from `eth_estimateGas`) → `eth_sendRawTransaction` to fork RPC → wait for receipt status `0x1`. A revert stops the sequence; never send the next tx.
4. **Record:** hashes + blocks, clone address, proposal id (`ProposalCreated` log), bond, `voteEnd` / `executeBy` (fork clock), in `workspace/ops/status.md`. Lifecycle → `proposed`.

## Execute

After `Approved` (vote ended, guardian cleared) and inside the execute window, on owner GO:
`sherwood --calldata-only proposal execute --id <id>` → Privy sign → raw broadcast → receipt `0x1` → log `executedAt` (fork clock) → lifecycle `executed`.

## Settle

After duration elapsed (`executedAt + duration`, fork clock), on owner GO:
`sherwood --calldata-only proposal settle --id <id>` → Privy sign → raw broadcast → log P&L / fees from the receipt → lifecycle `settled`. The proposer may settle early only after a hard **1h** floor from `executedAt`; earlier reverts `StrategyDurationNotElapsed()`. Do not settle early without owner GO.

## Watch lifecycle — terminal event is Settled

`proposed → voting → guardian → approved → executed → settle-ready → settling → settled → cooldown`. The 2026-09-20 run tore the watch down at Executed and had to re-arm it. Keep the `lifecycle-tick` / `settle-watch` routine polling `proposal show <id>` from Executed until **Settled** (or Rejected / Cancelled). Log each transition with fork-clock and wall-clock time.

## Live gates

Ops only if: Risk APPROVE, `fund.json` has real vault, RH basis known or haircut accepted, Privy sign→raw discipline.
