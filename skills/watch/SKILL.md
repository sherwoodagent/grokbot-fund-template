---
name: watch
description: Use while a proposal is open to report state changes until Settled, flag settle readiness and the weekend window, and write the postmortem.
---
# Watch

Commands and state names: Sherwood skill → `Governance` and `Phase 6: Monitor & Communicate`. Use chain time (`block.timestamp`), not the wall clock. Writes below use root `--calldata-only`, then sign/broadcast in order.

Each tick:
1. `sherwood proposal show <id> --vault <vault>` (or `sherwood proposal list --vault <vault>`). Message the owner **only on a change** (Pending → GuardianReview → Approved / Rejected / Expired → Executed → Settled, or Cancelled) or when something needs a decision.
2. Pending: this is the only window where the owner can veto. Remind the owner once, with the time left. After Pending, no veto.
2b. GuardianReview: check coverage every tick. In `proposal show`, read the **Guardian Coverage** section: WOOD locked per guardian and in total, its USD value, and covered vs required (required is in the vault asset, e.g. `60 USDG`). Don't use `effectiveMaxCapital`; it reads 0 until execute. The review is short (30 min on 4663, last 3 min locked), so if the status is still `NONE` or `PARTIAL` halfway through, tell the owner once with Review End. Coverage comes from the team guardian for cohort vaults; you don't recruit guardians.
2c. Approved (review over): execute only on the owner's yes. First `sherwood proposal execute --id <id> --vault <vault> --dry-run`. It fails when coverage is short: `NONE` means execute would revert `InsufficientApproveCoverage`, so don't send it. Tell the owner and offer cancel → wait out the cooldown that `proposal cancel` prints → repropose. `PARTIAL` deploys a proportional share; tell the owner the share and pass `--allow-partial-coverage` only on a yes. Then `sherwood --calldata-only proposal execute --id <id> --vault <vault>` → sign/broadcast before Execute By.
3. Executed: create the `position-<id>` routine from `workspace/proposals/<id>/plan.md` (see `routines.md`) and tell the owner its schedule once. Then track the kill criteria from `draft.md` (and the `--max-drawdown-bps` you set), plus price staleness and Morpho withdrawable liquidity. Flag breaches.
4. Settle readiness: settlement opens no earlier than **1 hour after execution**, then by early settle or duration end. Ask the owner. **Settle only on a yes:** `sherwood --calldata-only proposal settle --id <id> --vault <vault>`. For Portfolio, never settle inside the weekend StalePrice window (`sources get calendar`). Wait for the session.
5. A Portfolio rebalance back to the frozen weights is not a new proposal. New weights mean settle, then propose again.
6. Abort (when `fund.json` `attest` is true): on any abort, attest the kill with the thesis UID from `attestations.json` (0 or omitted if none):
   `sherwood --calldata-only attest kill --vault <vault> --proposal-id <id> --reason <code> --note "<one line why>" --thesis-uid <uid>` → sign/broadcast. Reason codes:
   - owner veto or owner cancel → `human_override`
   - Rejected or Expired (the vote or guardian review did not pass) → `vote_failed`
   - a kill criterion, drawdown, staleness or liquidity breach → `risk_check`
   - you cancel the proposal yourself because the thesis no longer holds (with the owner's yes) → `self_abort`
   - anything else → `other`
   It rides on the yes for the abort; if there was no yes in this chat (e.g. Expired), ask for one first. Save the kill UID in `attestations.json`. On failure, log it and continue.
7. After Settled, only if the owner wants it: `sherwood --calldata-only attest outcome --vault <vault> --proposal-id <id> --pnl-bps <n> --max-drawdown-bps <n> --note "…" --thesis-uid <uid>` (settlement events already record the result on chain). `attest execution` is optional and off by default.
8. Terminal (Settled / Rejected / Expired / Cancelled): `sherwood --calldata-only proposal reclaim-bond --id <id> --vault <vault>` (with a yes), write one postmortem line in `workspace/journal.md` (result, what the data got right or wrong, next idea), then disable `proposal-watch` and delete `position-<id>`. If it ended in any kill (Rejected, Expired, Cancelled, or an abort), the next proposal needs a fresh owner yes: ask, don't assume.
