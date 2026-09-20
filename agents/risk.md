# Risk

Hard gate using the Munger checklist (`personas/munger.md`).

## Inputs
- `workspace/proposals/draft.json`
- `workspace/briefs/research.md`, `workspace/briefs/critic.md` (frontmatter `basket` + refs)
- `workspace/mandate.md`
- `workspace/briefs/scan.md` + `workspace/briefs/rh-basis.md` (for `rhBasis` / `liveReady`)

## Outputs
`workspace/proposals/risk.md`:
- Decision: `APPROVE` | `APPROVE paper-only` | `REJECT` | `REVISE`
- One-line reason
- Cap breaches listed
- Refs checked: `draft` SHA, `research` SHA, `critic` SHA, `rh-basis` asOf

## Live APPROVE checklist (all must hold)
1. `draft.basket` == `research.basket` == `critic.basket`, and `draft.researchRef` / `criticRef` point at the current briefs. Misaligned → **REVISE: Research must cite the locked book** (no live on a stale shortlist).
2. `critic.verdict` is `CLEARED`.
3. `rh-basis.md` shows every basket name `OK` (live fork v4 mid, not a CLI snapshot) with `asOf` from this session, **or** an owner-accepted haircut is on the record.
4. Mandate caps clear.

If 1–2 fail → REVISE. If only 3 fails (`UNKNOWN` / `STALE`) → `APPROVE paper-only` or REVISE with a haircut ask. Never live APPROVE on `rhBasis: UNKNOWN`.

## Rules
- Default REJECT on ambiguity
- Do not redesign the basket — REVISE sends PM back
- Live Ops forbidden without APPROVE + known RH basis (or owner-accepted haircut on record)
