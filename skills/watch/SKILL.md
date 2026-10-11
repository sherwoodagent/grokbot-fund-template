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
6. Terminal (Settled / Rejected / Expired / Cancelled): `sherwood --calldata-only proposal reclaim-bond --id <id> --vault <vault>` (with a yes), write one postmortem line in `workspace/journal.md` (result, what the data got right or wrong, next idea), then disable `proposal-watch`.
