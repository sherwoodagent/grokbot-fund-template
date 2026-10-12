---
name: watch
description: Use while a proposal is open to report state changes until Settled, flag settle readiness and the weekend window, and write the postmortem.
---
# Watch

Commands and state names: Sherwood skill → `Governance` and `Phase 6: Monitor & Communicate`. Use chain time (`block.timestamp`), not the wall clock. Writes below use root `--calldata-only`, then sign/broadcast in order.

Each tick:
1. `sherwood proposal show --id <id> --vault <vault>` (or `sherwood proposal list --vault <vault>`). Message the owner **only on a change** (Pending → GuardianReview → Approved / Rejected / Expired → Executed → Settled, or Cancelled) or when something needs a decision.
2. Pending: this is the only window where the owner can veto. Remind the owner once, with the time left. After Pending, no veto.
3. Executed: track the kill criteria from `draft.md` (and the `--max-drawdown-bps` you set), plus price staleness and Morpho withdrawable liquidity. Flag breaches.
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
8. Terminal (Settled / Rejected / Expired / Cancelled): `sherwood --calldata-only proposal reclaim-bond --id <id> --vault <vault>` (with a yes), write one postmortem line in `workspace/journal.md` (result, what the data got right or wrong, next idea), then disable `proposal-watch`.
