# Ops cookbook: coverage locks, keyless keeper calls, proposal timeline

Desk recipes that the Sherwood skill (`https://sherwood.sh/skill.md`, v0.23.2, CLI ≥ 0.90.4) does not spell out. Read the skill first. Guardian mechanics live in the Sherwood **`guardian`** skill (`github.com/sherwoodagent/skill` → `skills/guardian/SKILL.md`, long form `skills/network-guardian/SKILL.md`). Owner recovery lives in **`skills/vault-owner/SKILL.md`**. The `sherwood.sh/skills/...` paths for those two 404, so use the GitHub repo.

Fork RPC: `https://api.sherwood.sh/tenderly/rpc` (chain `9994663`). It rejects every `tenderly_*` / `evm_*` method, so there is **no time travel**. The fork clock is **erratic**: it has run about 6× fast, stalled, and jumped +7,081 s in one block. Deadlines are `block.timestamp`, so read the latest block timestamp against `voteEnd` / `reviewEnd` / `executeBy` and put a monitor on each step. Never schedule from the wall clock.

Shared rule for every step: `eth_call` + `eth_estimateGas` the exact calldata against `latest` first. If it reverts, **stop**. Decode the selector (Sherwood `ERRORS.md`, or `cast 4byte`), log it in `workspace/ops/status.md`, and do not broadcast.

---

## 1. Cancel-lock: `ApproveLockBelowFloor` and friends

### Symptom

The guardian Approve (`GuardianRegistry.voteOnProposal(governor, id, Approve, lockWood)`) fails estimateGas with **`0x13b33cc4` = `ApproveLockBelowFloor()`**, bubbled from `ExposureLedger.recordApproval`.

### Cause

The booked lock is clamped to the guardian's free budget:

```
free = kNumerator × guardianStake − openExposure(guardian)
```

The call reverts when the booked lock is zero or its USD value is below `floorUsd` (see guardian skill §5). The usual trap is **stale Approve locks from cancelled proposals**:

- An Approve lock stays in `openExposure` after its proposal is cancelled. It keeps counting until the approval's **challenge window** has passed.
- `retireApproval(governor, id, guardian)` is the only unwind. It reverts **`ChallengeWindowOpen()` (`0xfa7bc547`)** until that window closes, which can be weeks away. Nothing on the sanctioned RPC shortens it.
- Result: after a cancel, the guardian's free coverage can be far below its stake, or zero.

### Diagnose

```bash
cast call $EXPOSURE_LEDGER "openExposure(address)(uint256)" $GUARDIAN --rpc-url $RPC
cast call $EXPOSURE_LEDGER "kNumerator()(uint256)"          --rpc-url $RPC
cast call $EXPOSURE_LEDGER "woodPriceX8()(uint256)"         --rpc-url $RPC
cast call $GOVERNOR "getRequiredCoverage(uint256)(uint256)" $ID --rpc-url $RPC
sherwood guardian status   # own stake
```

Free coverage in USD ≈ `free WOOD × woodPriceX8 / 1e8`. Compare it with the proposal's required coverage in USD. Tier 2 = **full notional** (`maxCapital`).

### Fix (pick one, log which)

1. **Size to free coverage.** Size new proposals so required coverage ≤ free coverage, minus a few % margin for WOOD price moves. The size cap is free guardian stake × WOOD price, not vault size.
2. **Add guardian stake** (`sherwood guardian stake <amount>`), but keep liquid WOOD ≥ the next proposer-bond quote (`ExposureLedger.proposerBondWood(asset, requiredCoverage)`, ≈1% of coverage). Don't stake everything.
3. **Wait out the challenge window**, then `retireApproval`. Only realistic if the window is close.

Don't retry the same `lockWood`. This is a refusal, not a transient error. **Prevention:** avoid cancelling a proposal after an Approve lock is booked unless you accept that budget staying stranded for the challenge window.

### Related reverts

| Selector | Error | Meaning | Action |
|---|---|---|---|
| `0x0457efb9` | `ReviewNotOpen()` | `voteOnProposal` before the review is opened | Send permissionless `openReview(governor, id)` (callable from `voteEnd`), wait for receipt `0x1`, then vote. Check `getReviewState(governor, id)` → `opened`. estimateGas targets the **next** block, so right at `voteEnd` it can still revert; wait a few minutes and re-estimate. |
| `0xf7448092` | `InsufficientApproveCoverage()` | Execute with no booked Approve coverage (empty approver set or zero aggregate) | The review closed without a usable Approve. A partial book does not revert; it scales `maxCapital` down pro rata. This proposal cannot execute: cancel and re-propose, sized to free coverage (§1 fix 1), with an Approve booked during review. |
| `0x13b33cc4` | `ApproveLockBelowFloor()` | Booked lock zero or below `floorUsd` | See above. |
| `0xfa7bc547` | `ChallengeWindowOpen()` | `retireApproval` before the challenge window ends | Wait. No shortcut. |

---

## 2. Keyless keeper + guardian calls (viem + Privy)

In CLI 0.90.4, `sherwood proposal open-reviews` and `resolve-reviews` ignore `--calldata-only` and always need `PRIVATE_KEY`. `resolveProposalState` has no CLI command. Don't configure a key. Encode these four calls with viem, then Privy sign → raw broadcast:

| Call | Target |
|---|---|
| `openReview(address governor, uint256 id)` | GuardianRegistry |
| `voteOnProposal(address governor, uint256 id, uint8 support, uint256 lockWood)` (support `1` = Approve, `2` = Block; lockWood in WOOD wei) | GuardianRegistry |
| `resolveReview(address governor, uint256 id)` | GuardianRegistry |
| `resolveProposalState(uint256 id)` | the vault's governor (`vault.governor()`) |

```js
import { createPublicClient, http, encodeFunctionData, parseAbi } from "viem";
const pc = createPublicClient({ transport: http(RPC) });
const abi = parseAbi([
  "function openReview(address governor, uint256 proposalId)",
  "function voteOnProposal(address governor, uint256 proposalId, uint8 support, uint256 lockWood)",
  "function resolveReview(address governor, uint256 proposalId)",
  "function resolveProposalState(uint256 proposalId)",
]);
const data = encodeFunctionData({ abi, functionName: "openReview", args: [GOVERNOR, ID] });
const tx = { account: AGENT, to: GUARDIAN_REGISTRY, data };
await pc.call(tx);                       // revert → decode, log, STOP
const gas = await pc.estimateGas(tx);    // estimates against the NEXT block
const nonce = await pc.getTransactionCount({ address: AGENT, blockTag: "pending" });
// Privy eth_signTransaction { to, data, value: 0, chain_id: 9994663, nonce, gas_limit: gas * 12n / 10n, gas price }
// → eth_sendRawTransaction(signed) → wait for receipt status 0x1
```

The CLI's `review-vote` has a `--calldata-only` branch in the 0.90.4 source, but the desk hasn't tested it. Treat the viem path as primary.

## 3. Proposal timeline, expiry, terminal cleanup

### Monitor the timeline (fork clock)

Right after propose, read `voteEnd`, `reviewEnd` and `executeBy` from `sherwood proposal show <id>` (or governor/registry getters) and log them in `workspace/ops/status.md` as chain timestamps. Don't assume lengths, and don't convert them into wall-clock ETAs: the fork clock can reach a deadline hours early or late. Redemptions lock as soon as the proposal is **Pending**; the vault checks `governor.openProposalCount() != 0`, which takes no argument. Tell depositors at propose time.

| Phase | When | Who / call | Gate |
|---|---|---|---|
| Voting | propose → `voteEnd` | Pending; depositors vote | none from Ops |
| Open review | at/after `voteEnd` | anyone: `openReview(governor, id)` (keyless, §2) | estimateGas → receipt `0x1` |
| Approve | right after openReview, before the late-vote lockout | guardian: `voteOnProposal(governor, id, 1, lockWood)` (keyless, §2) | lock ≤ free budget; estimateGas |
| Review end | `reviewEnd` | wait | none |
| Resolve | after `reviewEnd` | anyone: `resolveReview(governor, id)`, then `resolveProposalState(id)` → Approved (keyless, §2) | estimateGas |
| Execute | Approved, **before `executeBy`** | proposer: `proposal execute` (owner GO) | estimateGas |
| Settle | per the skill's settle gates | `proposal settle` (owner GO) | estimateGas |
| Cleanup | any terminal state | `resolveProposalState(id)` if `openProposalCount() != 0`, then `proposal reclaim-bond` (below) | estimateGas |

Monitor each step on chain time. Poll the latest block `timestamp` every ≤15 min, and every ≤5 min near a boundary. Fire the step on the first tick past its boundary. After a clock jump, re-read the proposal state, lockout and `executeBy` before acting. A missed boundary can cost the whole proposal.

### If the proposal expires

Execute after `executeBy` reverts `ExecutionWindowExpired()` (`0x3c09dd11`), and the proposal ends `Expired`. **Redemptions stay locked.** They locked at Pending, and `openProposalCount()` is only decremented lazily, so they stay locked after expiry until someone calls `resolveProposalState(id)`.

1. Stop. Don't retry execute. `cancel` / `settle` / `unstick` don't apply to Expired.
2. **Required: `resolveProposalState(id)`** on the governor (keyless, §2). It is permissionless. Confirm `openProposalCount() == 0` and `vault.redemptionsLocked() == false`. This also frees the slot (otherwise a new propose reverts `VaultHasOpenProposal()`, `0x53f85547`).
3. **Reclaim the bond** (see below). The full bond comes back after expiry, with nothing forfeited.
4. Re-check free guardian coverage (§1). Any Approve lock booked on the expired proposal stays in `openExposure` until its challenge window passes.
5. Re-propose sized to **free** coverage. Keep liquid WOOD ≥ the new bond quote (bond reserve) before staking any more.
6. Re-arm the monitor with the new proposal's chain deadlines.

### Reclaim the proposer bond (every terminal state)

After Cancelled / Rejected / Expired / Settled, `SyndicateGovernor.reclaimProposerBond(id)` returns the full bond from escrow to whoever posted it. It is permissionless. For an **executed → Settled** proposal, it only works after `executedAt + strategyDuration + challengeWindow`.

```bash
sherwood --calldata-only proposal reclaim-bond --id <id> --vault <vault>
```

This works keyless in 0.90.4: it emits one governor tx, and you Privy sign → raw broadcast it. The viem equivalent is `encodeFunctionData({ abi: parseAbi(["function reclaimProposerBond(uint256 proposalId)"]), functionName: "reclaimProposerBond", args: [ID] })` to the governor. Log the WOOD delta; it should equal the bond pulled at propose.
