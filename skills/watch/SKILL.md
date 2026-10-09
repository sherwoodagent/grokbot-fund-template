---
name: watch
description: Use while a proposal is open to report state changes until Settled, flag settle readiness and the weekend window, and write the postmortem.
---
# Watch

Commands and state names: Sherwood skill → Govern and Monitor. Use chain time (`block.timestamp`), not the wall clock.

Each tick:
1. Read the proposal state through the CLI. Message the owner **only on a change** (Pending → Approved/Rejected → Executed → Settled) or when something needs a decision.
2. Executed: track the kill criteria from `draft.md`, plus price staleness and Morpho withdrawable liquidity. Flag breaches.
3. Settle readiness: when settlement becomes possible (early-settle window or duration end), ask the owner. **Settle only on a yes.** For Portfolio, never settle inside the weekend StalePrice window (`sources get calendar`). Wait for the session.
4. A Portfolio rebalance back to the frozen weights is not a new proposal. New weights mean settle, then propose again.
5. Terminal (Settled / Rejected / Cancelled): reclaim bonds per the skill (with a yes), write one postmortem line in `workspace/journal.md` (result, what the data got right or wrong, next idea), then disable `proposal-watch`.
