---
name: sherwood-ops
description: Use when minting an ERC-8004 identity, staking WOOD, creating a Sherwood vault, or proposing/executing/settling a PortfolioStrategy, MorphoSupply or ConcentratedLiquidity strategy on the RH fork with Privy (sign then raw broadcast), including proposer bond + guardian coverage sizing.
---

# Sherwood Ops (beta)

Canonical detail: `docs/SETUP.md`. Tracks Sherwood skill v0.23.2 (`https://sherwood.sh/skill.md`).

- CLI: `@sherwoodagent/cli` **≥ 0.90.4** (`sherwood --version`; else `npm i -g @sherwoodagent/cli@0.90.4`).
- Chain `9994663`, RPC `https://api.sherwood.sh/tenderly/rpc`. The old Tenderly vnet URL is dead.
- The RPC serves reads + `eth_sendRawTransaction` only. It rejects `eth_sendTransaction` and **all `tenderly_*` / `evm_*` methods**: no time travel, so proposal windows run in **real time** (plan them, see `docs/ops-cookbook.md` §2).

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
→ 1 ETH + 15k WOOD + 1k USDG; 1 / address / IP / 24h. The 15k covers the 10k owner stake plus only a **small-book** proposer bond.

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

`Pending (voting, optimistic) → GuardianReview → Approved → Executed → Settled` (+ `Rejected` / `Cancelled`). CLI keys that resolve on the fork: `portfolio`, `morpho-supply`, `concentrated-liquidity`, `launchpad`. `sherwood strategy list` is the source of truth; `workspace/strategies.md` decides which ones the desk may use (starter = `portfolio`). Pre-commit execute + settle. One live strategy at a time.

## Coverage + proposer bond (size before you propose)

- **Tier 2 is the default** for uncertified calls and costs **full-notional** coverage: `requiredCoverage = Σ(cap_i × boundBps_i) / 10_000` with `boundBps = 10_000`, i.e. `requiredCoverage = maxCapital`. Only certified tier 0/1 adapters are cheaper. Tier 2 is permissionless: a price, not a prohibition.
- **Size cap = free guardian stake × WOOD price, not vault size.** Guardians must book Approve coverage ≥ `requiredCoverage` before execute. Guardian free budget = `kNumerator × guardianStake − openExposure` (ExposureLedger). Stale locks from cancelled proposals keep counting (`docs/ops-cookbook.md` §1).
- **Proposer bond ≈ 1% of coverage, in WOOD.** `propose` pulls it into `ProposerBondEscrow`, separately from the 10k owner stake and any guardian stake. Quote `ExposureLedger.proposerBondWood(asset, requiredCoverage)` and log it; never hard-code a WOOD number.
- The proposer wallet must **hold** the bond liquid. Allowance alone fails `InsufficientProposerBondWood`. **Don't stake everything into sWOOD**: keep a bond reserve ≥ the next quote.

## Propose — one-shot recipe (Privy, no local key)

Verified 2026-09-20 on fork `9994663` (proposal #1: cloneAndInit → governor.propose, both `0x1`).

1. **Re-quote basis** for the draft basket at propose size (v4 quoter, see `agents/scanner.md`). Any name `STALE` → stop, back to Risk.
2. **Calldata:**
   ```bash
   sherwood --calldata-only strategy propose portfolio \
     --vault <fund.json vault> --proposer <fund.json agent> \
     --amount <USDG> --asset USDG \
     --tokens MSFT,GOOGL,NVDA,AMZN,QQQ --weights 2500,2000,2000,2000,1500 \
     --swap-routes v4:3000:60,v4:3000:60,v4:3000:60,v4:3000:60,v4:3000:60 \
     --name "<draft name>" --description "<draft rationale>" --duration 7d
   ```
   Same keyless shape for `morpho-supply` / `concentrated-liquidity` (their flags below). `--proposer` is required. Emits `txs` in order:
   1. `WOOD.approve(bondEscrow)`, **only when the existing allowance does not already cover the bond**.
   2. `StrategyFactory.cloneAndInitDeterministic` (predicted `clone` + `salt`).
   3. `governor.propose`, which pulls the proposer bond (`ProposerBondEscrow.lockBond` → `transferFrom`).

   The CLI preflights first: the proposer is a registered agent, the vault is not paused, the vault balance ≥ `--amount`, and the wallet holds the quoted bond. If the clone reverts, do **not** send propose.
3. **Per tx, in order:** Privy `eth_signTransaction` (`chain_id: 9994663`, nonce from `eth_getTransactionCount`, gas from `eth_estimateGas`) → `eth_sendRawTransaction` to fork RPC → wait for receipt status `0x1`. A revert stops the sequence; never send the next tx.
4. **Record:** hashes + blocks, clone address, proposal id (`ProposalCreated` log), bond, `voteEnd` / `executeBy` (fork clock), in `workspace/ops/status.md`. Lifecycle → `proposed`.

## Execute

After `Approved` (vote ended, guardian cleared) and inside the execute window, on owner GO:
`sherwood --calldata-only proposal execute --id <id>` → Privy sign → raw broadcast → receipt `0x1` → log `executedAt` (fork clock) → lifecycle `executed`.

## Settle — two gates (do not conflate)

Desk friction (2026-09-22): Ops treated `executedAt + duration` (often 7d) as the **only** settle gate. Wrong.

| Who | When (fork clock) | Call |
|-----|-------------------|------|
| **Proposer** | Anytime while `Executed`, after hard **~1h** floor from `executedAt` (`MIN_STRATEGY_DURATION_BEFORE_SELF_SETTLE`) | `settleProposal` via `proposal settle` |
| **Permissionless** | After **full** strategy duration (`executedAt + duration`) | same `settleProposal` — anyone |
| **Owner emergency** | Stuck unwind path only | `emergencySettleWithCalls` / vault-owner skill — not the happy path |

- **`earliestSettle` / settle-ready in `proposal show`** = the **permissionless** line (`executedAt + duration`). Proposer early-settle is a **separate** path; do not wait for `earliestSettle` if you are the proposer and ≥1h has passed.
- **Owner GO still required** on this desk before any settle broadcast (beta discipline), even when the chain would allow permissionless settle.
- Earlier than the 1h floor → `StrategyDurationNotElapsed()`.

Recipe on owner GO:
`sherwood --calldata-only proposal settle --id <id>` → Privy sign → raw broadcast → log P&L / fees → lifecycle `settled`.

### Troubleshooting — `StalePrice()` on settle / estimateGas

First settle attempt (2026-09-22) failed `estimateGas` with selector **`0x19abf40e`** = `StalePrice()`. That is **push-feed age** (`MAX_PUSH_PRICE_AGE`, ~26h), **not** the pool-vs-cash RH basis band.

1. `eth_call` the settle calldata first. Retry broadcast **only** when the call is green.
2. On fail, dump tip block time vs each basket feed `latestRoundData().updatedAt` (and round id). Wait for feeds or escalate to Sherwood. You cannot refresh them through the RPC (no `tenderly_*` / `evm_*`). Do not “fix” basis by widening pool quotes.
3. Same selector can also block `rebalanceDelta()` — same feed-age gate.

## rebalanceDelta ≠ new propose

While PortfolioStrategy is **`Executed`**, the proposer may call **`rebalanceDelta()`** (no args, proposer-only). It snaps drift back to the **frozen init weights** voters approved — sell overweight / buy underweight, priced off per-slot Chainlink push feeds. It does **not** re-weight the book mid-proposal (`WeightsFrozen` / `RoutesFrozen` if you try). “Rebalance” ≠ new `strategy propose`. Slippage may only be tightened, never loosened.

## Watch lifecycle — terminal event is Settled

`proposed → voting → guardian → approved → executed → settle-ready → settling → settled → cooldown`. The 2026-09-20 run tore the watch down at Executed and had to re-arm it. Keep the `lifecycle-tick` / `settle-watch` routine polling `proposal show <id>` from Executed until **Settled** (or Rejected / Cancelled). Log each transition with fork-clock and wall-clock time.

## Live gates

Ops only if: Risk APPROVE, `fund.json` has real vault, RH basis known or haircut accepted, Privy sign→raw discipline.


## MorphoSupply / ConcentratedLiquidity (CLI keys, fork `9994663`)

Advanced / growth opt-in only (`docs/advanced-growth.md`). Starter default remains PortfolioStrategy. Market/pool evidence: `workspace/ops/morpho-cl-cookbook.md`.

- **MorphoSupply:** `sherwood --calldata-only strategy propose morpho-supply --vault … --proposer … --market-id <bytes32> --amount <n> [--morpho <addr>]`. The CLI runs the init checks before any tx: `MorphoNotAllowed` (Morpho on the vault's TierRegistry), `LoanAssetMismatch` (loan token ≠ vault asset), `MarketNotCreated`. `updateParams` reverts `NoTunableParams`. Settle is all-or-revert: a high-utilization market can block settlement until liquidity returns.
- **ConcentratedLiquidity:** `strategy propose concentrated-liquidity` is a Uniswap **V3** range funded by a Morpho borrow against vault-asset collateral. It is **not** v4 equity LP. Init checks every counterparty; anything not allowlisted fails `CounterpartyNotAllowed` (a registry-owner action; tell the owner, don't retry). Flags and the documented USDG/WETH + spUSDG example: Sherwood skill § ConcentratedLiquidityStrategy.
- Both are tier 2: full-notional coverage + bond, same as above.

### Critic gate

- Morpho without a verified market id (cookbook or skill example) → **BLOCKED**.
- CL framed as v4 equity LP, or failing the counterparty preflight → **BLOCKED**.

## Guardian review + stuck proposals (upstream skills)

Source: `github.com/sherwoodagent/skill` (the `sherwood.sh/skills/...` paths 404).

- **Guardian** (stake, `openReview`, Approve/Block, `lockWood` sizing, `ApproveLockBelowFloor`): `skills/guardian/SKILL.md`, long form `skills/network-guardian/SKILL.md`. `openReview` is permissionless and must land before the Approve vote (`ReviewNotOpen` otherwise). With Privy: `cast calldata` → sign → raw broadcast.
- **Vault owner** (stuck Executed proposal: `unstick`, or bonded `emergencySettleWithCalls` → `finalizeEmergencySettle`): `skills/vault-owner/SKILL.md`. Owner-only.
- Desk recipes (stale cancel locks, `InsufficientApproveCoverage`, real-time timeline, expired proposals): `docs/ops-cookbook.md`.
