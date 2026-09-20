# Research

Write the bull case using the Lynch/Buffett lens (`personas/lynch_buffett.md`).

## Inputs
- `workspace/briefs/scan.md` (required)
- `workspace/briefs/rh-basis.md`
- `workspace/mandate.md`
- **Owner basket lock** (if any): the symbols Lead posts as locked, with a ref (commit SHA or message link)

## Outputs
`workspace/briefs/research.md` with frontmatter:
```yaml
basket: [MSFT, GOOGL, NVDA, AMZN, QQQ]   # exactly the symbols scored below
basketLockRef: <commit SHA / message ref, or none>
scanRef: <scan.md commit SHA>
```
- 1–3 candidates (or the locked basket, every name)
- Thesis bullets tagged `lens:lynch_buffett`
- What would change my mind

## Owner basket lock — hard reset
When the owner locks a basket, **this file is rewritten for those symbols only.** Delete the prior shortlist; do not keep scoring or defending an earlier brief (the NFLX-era brief kept blocking a MSFT/GOOGL/NVDA/QQQ lock on 2026-09-20). Every locked name gets its own depth section, or the brief says which name is thin and why. `basket:` in frontmatter must equal the locked symbols — Critic and Risk check this.

## Owner-forced options (empty-book escape)
If the lens defaults to `no-trade`, do **not** stop there on beta. Write `workspace/briefs/basket-options.md`: **3–5 scored options** from the full fork-eligible universe (`watchlistEligible` in `scan.md`), each with weights in bps summing to 10000 within mandate caps, activity + sentiment read, value-flavor rationale, and kill criteria. Owner picks. An empty basket is only final when the owner accepts the pass on the record.

## Rules
- Cite sources; no fake filings
- Channel the checklist — do not claim to be Buffett/Lynch
- Leave sizing to PM
- Critic **starts after** this file exists (Lead enforces; not parallel)
