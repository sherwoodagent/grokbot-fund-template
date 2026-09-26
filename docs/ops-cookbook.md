# Ops cookbook: coverage locks + real-time proposal timeline

Desk recipes that the Sherwood skill (`https://sherwood.sh/skill.md`, v0.23.2, CLI ≥ 0.90.4) does not spell out. Read the skill first. Guardian mechanics live in the Sherwood **`guardian`** skill (`github.com/sherwoodagent/skill` → `skills/guardian/SKILL.md`, long form `skills/network-guardian/SKILL.md`). Owner recovery lives in **`skills/vault-owner/SKILL.md`**. The `sherwood.sh/skills/...` paths for those two 404, so use the GitHub repo.

Fork RPC: `https://api.sherwood.sh/tenderly/rpc` (chain `9994663`). It rejects every `tenderly_*` / `evm_*` method, so there is **no time travel**. Every window below runs in real time.

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

## 2. Expired / no-time-travel recipe

### Plan the timeline before proposing

Read the windows from `sherwood proposal show <id>` (or governor/registry getters) right after propose. Convert them to the owner's local time and put them in `workspace/ops/status.md`. Don't assume lengths. The live stack's `reviewPeriod` is 86400 s, so budget several real days end to end.

| Phase | When | Who / call | Gate |
|---|---|---|---|
| Voting | propose → `voteEnd` | Pending; depositors vote | none from Ops |
| Open review | at/after `voteEnd` | anyone: `openReview(governor, id)` | estimateGas → receipt `0x1` |
| Approve | right after openReview | guardian: `voteOnProposal(governor, id, Approve, lockWood)` | lock ≤ free budget; estimateGas |
| Review end | `reviewEnd` | wait | none |
| Resolve | after `reviewEnd` | anyone: `resolveReview` (or `sherwood proposal resolve-reviews --vault …`), then the governor's state resolves to Approved | estimateGas |
| Execute | Approved, **before `executeBy`** | proposer: `proposal execute` (owner GO) | estimateGas |
| Settle | per the skill's settle gates | `proposal settle` (owner GO) | estimateGas |

Arm the routine (`lifecycle-tick` / `settle-watch`) with wake-ups at `voteEnd`, `reviewEnd`, and well before `executeBy`. With no time travel, a missed tick can cost the whole proposal.

### If the proposal expires

Execute after `executeBy` reverts `ExecutionWindowExpired()` (`0x3c09dd11`), and the proposal ends `Expired`. The vault was never locked.

1. Stop. Don't retry execute.
2. Free the vault slot. If a new propose would revert `VaultHasOpenProposal()` (`0x53f85547`), flush the state (`resolveProposalState(id)`). If it is still Approved-but-unexecuted and holding the slot, the proposer cancels it (`sherwood proposal cancel --id <id>`).
3. Re-check free guardian coverage (§1). Any Approve lock booked on the expired proposal stays in `openExposure` until its challenge window passes.
4. Re-propose sized to **free** coverage. Keep liquid WOOD ≥ the new bond quote (bond reserve) before staking any more.
5. Re-plan the timeline and arm the wake-ups.
