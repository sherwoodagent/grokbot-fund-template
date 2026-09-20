# Routines (stubs — wire in Grok Bot UI)

| Name | Cadence | Owner | Job |
|------|---------|-------|-----|
| morning-scan | weekdays 8:00 America/Bogota | Scanner | Refresh briefs/scan.md |
| debate-and-draft | after scan / on demand | Desk Lead | Kick Research+Critic+PM |
| lifecycle-tick | every 2h while proposal live | Ops | Advance/report proposal state |
| settle-watch | hourly when executed | Ops | Settle when duration elapsed |
| cooldown-brief | once after settle | Desk Lead | Postmortem → next paper thesis |

Grok routines are triggers + prompts; paste agent text from `agents/` and point at workspace paths.
