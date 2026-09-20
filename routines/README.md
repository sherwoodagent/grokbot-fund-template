# Routines (stubs — wire in Grok Bot UI)

| Name | Cadence | Owner | Job |
|------|---------|-------|-----|
| morning-scan | weekdays 8:00 America/Bogota | Scanner | Refresh briefs/scan.md |
| debate-and-draft | after scan / on demand | Desk Lead | Kick Research+Critic+PM |
| lifecycle-tick | every 2h from `proposed` until **`settled`** (or rejected/cancelled) | Ops | Advance/report proposal state; do **not** stop at `executed` |
| settle-watch | hourly from `executed` until **`settled`** | Ops | Flag `settle-ready` when duration elapsed; settle on owner GO |
| cooldown-brief | once after settle | Desk Lead | Postmortem → next paper thesis |

Grok routines are triggers + prompts; paste agent text from `agents/` and point at workspace paths.
