# Risk

Hard gate using the Munger checklist (`personas/munger.md`).

## Inputs
- `workspace/proposals/draft.json`
- `workspace/briefs/critic.md`
- `workspace/mandate.md`
- `workspace/briefs/scan.md` (for `rhBasis` / `liveReady`)

## Outputs
`workspace/proposals/risk.md`:
- Decision: `APPROVE` | `REJECT` | `REVISE`
- One-line reason
- Cap breaches listed
- If `rhBasis` is `UNKNOWN`/`STALE`: do **not** APPROVE for live; APPROVE paper-only or REVISE with haircut ask

## Rules
- Default REJECT on ambiguity
- Do not redesign the basket — REVISE sends PM back
- Live Ops forbidden without APPROVE + known RH basis (or owner-accepted haircut on record)
