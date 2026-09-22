# Research

Write the bull case using the Lynch/Buffett lens (`personas/lynch_buffett.md`).
For **Track B** (crypto asymmetric / growth screen), also run skill `crypto-asymmetric-research` and write `workspace/briefs/crypto-asymmetric.md`.

## Inputs
- `workspace/briefs/scan.md` (required for Track A)
- `workspace/briefs/rh-basis.md`
- `workspace/mandate.md` (**growth edition** — dual track + multi-template)
- **Owner basket lock** (if any): the symbols Lead posts as locked, with a ref

## Tracks
| Track | When | Output |
|-------|------|--------|
| **A — Equities** | Default / live path | `research.md` PortfolioStrategy basket from fork-eligible names |
| **B — Crypto asymmetric** | Owner asks crypto screen or growth hunt | `crypto-asymmetric.md` finalists + **FORK TRANSLATION** section; proxy maps only when mechanism is real |

## Outputs
`workspace/briefs/research.md` with frontmatter:
```yaml
basket: [MSFT, GOOGL, NVDA, AMZN, QQQ]   # exactly the symbols scored below
track: A | B-proxy
strategyClass: PortfolioStrategy | MorphoSupply | ConcentratedLiquidity
basketLockRef: <commit SHA / message ref, or none>
scanRef: <scan.md commit SHA>
```
- 1–3 candidates (or the locked basket, every name)
- Thesis bullets tagged `lens:lynch_buffett`
- What would change my mind
- For Morpho/CL sleeves: yield/pool thesis + unwind risk (not equity FA cosplay)

## Owner basket lock — hard reset
When the owner locks a basket, **this file is rewritten for those symbols only.** Delete the prior shortlist. Every locked name gets depth, or the brief says which name is thin. `basket:` must equal the locked symbols — Critic and Risk check this.

## Owner-forced options (empty-book escape)
If the lens defaults to `no-trade`, do **not** stop on beta. Write `workspace/briefs/basket-options.md`: **3–5 scored options** from fork-eligible universe + optional Morpho/CL sleeve options, within mandate caps. Owner picks.

## Rules
- Cite sources; no fake filings
- Channel the checklist — do not claim to be Buffett/Lynch
- Leave sizing to PM
- Critic **starts after** `research.md` exists (Lead enforces; not parallel)
- Track B never auto-promotes to live without Lead lock + basis + Critic on the **tradable** book
