# Session friction — Grok Fund Beta live loop (2026-09-20)

Notes from a full desk run: installer → widen universe → basket pick → paper/live gates → propose → execute → settle watch. Use this to harden template skills (`getting-started`, `sherwood-ops`, `agents/*`, `docs/SETUP.md`).

## What worked

- Linear SETUP gates (wallet → fund → roster → paper → live)
- Explicit owner GOs for propose and execute
- Artifact trail in `workspace/` (briefs, draft, risk, ops/status)
- Risk hard veto on thin Research depth
- Live Uniswap v4 quotes for RH basis (once used)

## Friction to fix

### 1. Strict personas on basket selection

When the owner locks a basket, Research/Critic kept scoring an *earlier* shortlist (e.g. NFLX-era brief after MSFT/GOOGL/NVDA/QQQ pick). Risk correctly blocked live on depth kill.

**Fix:** On owner basket lock, hard-reset `research.md` / `critic.md` to those symbols only — or refuse Risk **live** APPROVE until Research SHA cites the locked book. Persona lenses score *that* basket; they do not defend the prior shortlist.

### 2. RH basis — Scanner must quote the fork

First passes left `rhBasis: UNKNOWN` (cash proxy only) or relied on stale CLI `feedDeviationBps`. Live Uniswap **v4** quoter showed tight basis (max ~53 bps vs Fri cash).

**Fix:** Scanner skill requires a live fork mid (V4 quoter / StateView) per watchlist name, or fail closed with `UNKNOWN`. Do not leave basis discovery to Risk at the end of the loop.

### 3. Cadence enforcement (no false parallel)

Critic needs `research.md`; PM needs Critic. Mid-flight Lead had to re-order handoffs.

**Fix:** Desk Lead skill: explicit waits (Scanner → Research → Critic → PM → Risk → Ops). Parallel only when prompts allow.

### 4. Ops watch lifecycle

Vote/guardian watch was torn down at **Executed**; settle-watch had to be re-armed.

**Fix:** Watch routine terminal event is **Settled** (or reject/cancel), not Executed. Keep polling from Executed → settle-ready → Settled.

### 5. Empty-book attractor

Druckenmiller-lite + Burry defaulted to `no-trade` until the owner forced a widen + basket options.

**Fix:** For beta, document an **owner-forced basket options** path in Research/PM: produce 3–5 scored options from the full fork-eligible universe; empty basket only when owner accepts pass.

### 6. Privy propose recipe

Propose worked (clone → WOOD bond approve → governor.propose) but Ops reinvented sequencing.

**Fix:** Ship `skills/sherwood-ops` (or `docs/propose-portfolio.md`) one-shot checklist: re-quote → `--calldata-only strategy propose portfolio` → Privy `eth_signTransaction` → `eth_sendRawTransaction` per tx → update `ops/status.md`. Never `eth_sendTransaction` on chain `9994663`.

### 7. fund.json blank on soul refresh

Non-destructive template refresh wiped `fund.json` fields.

**Fix:** Getting-started / Lead: on blank `fund.json`, restore via `sherwood` / API resolve by subdomain — never leave empty placeholders after a live create.

### 8. X / sentiment connector

Scanner should use **whichever X (or social) connector is connected** on the bot. Do not hardcode a specific account. If none connected, flag the gap and continue with public web sources.

## Suggested skill edits (checklist)

| File | Change |
|------|--------|
| `agents/scanner.md` | Require fork V4 mid; X = any connected connector |
| `agents/research.md` | Reset brief on owner basket lock |
| `agents/critic.md` | Score locked basket only; depth kill if Research misaligned |
| `agents/pm.md` | Owner-forced options path; refuse empty without owner pass |
| `agents/risk.md` | Live needs aligned Research SHA + basis OK |
| `agents/ops.md` | Propose checklist; watch until Settled |
| `agents/desk-lead.md` / SETUP | Enforce waits; restore fund.json from chain |
| `skills/sherwood-ops` | One-shot propose + execute recipes |

## Resolution (2026-09-20)

| # | Friction | Fixed in |
|---|----------|----------|
| 1 | Strict personas on basket lock | `agents/research.md` (hard reset + `basket:` frontmatter), `agents/critic.md` (score locked basket only; `REJECT-QUALITY` on mismatch), `agents/pm.md` (`basket` + refs in draft), `agents/risk.md` (live needs aligned basket + refs), `agents/desk-lead.md` (lock → restart from Research) |
| 2 | RH basis — Scanner must quote the fork | `agents/scanner.md` (v4 quoter method, fail closed, `rh-basis.md` output), `docs/SETUP.md` |
| 3 | Cadence enforcement | `agents/desk-lead.md` (waits table), `docs/SETUP.md` |
| 4 | Ops watch lifecycle | `agents/ops.md`, `skills/sherwood-ops`, `routines/README.md` (terminal = Settled) |
| 5 | Empty-book attractor | `agents/research.md` (`basket-options.md` path), `agents/pm.md` (owner pass required), `agents/desk-lead.md` |
| 6 | Privy propose recipe | `skills/sherwood-ops` (one-shot propose / execute / settle), `agents/ops.md` |
| 7 | fund.json blank on soul refresh | `skills/getting-started`, `docs/SETUP.md`, `agents/desk-lead.md` (restore from chain via API) |
| 8 | X / sentiment connector | `agents/scanner.md` (any connected connector; `socialSource: none` fallback) |

Also added (separate finding): ERC-8004 identity mint as installer step 2 — the run created the fund with `agentId 0`.

## Context from this run

- Fund: Grok Fund Beta · vault on chain `9994663` · proposal **#1** Executed (basket 3, 500 USDG, 7d)
- Settle watch active; settle gated on owner GO

## Session friction — growth mandate + settle/rebalance (2026-09-22)

Filed to COO for template push 2026-09-22.

| # | Friction | Fix direction |
|---|----------|---------------|
| 9 | Mandate PortfolioStrategy-only blocked growth | Growth-edition mandate: Morpho + CL satellites; Track A/B dual research |
| 10 | Crypto research prompt not in template | Skill `crypto-asymmetric-research` + Research/Critic wiring |
| 11 | Ops watch used +7d as only settle gate | Proposer early-settle (≥~1h); permissionless after duration; owner GO still required |
| 12 | Desk confused rebalance vs re-propose | Document `rebalanceDelta` = drift to frozen init weights only |
| 13 | Settle `StalePrice` misread as pool basis | Feed-age gate; eth_call before retry; snapshot tip vs updatedAt |
| 14 | Roster bots cannot push GitHub | Lead/Ops commit path or gh auth; document in SETUP |
| 15 | Morpho/CL allowed but no market/pool cookbook | Ops discovery recipe or mark design-only until IDs published |
| 16 | Standing: Lead notifies COO on template friction | Desk Lead profile memory — ping COO to PR template |

## Resolution (2026-09-22) — template PR

| # | Friction | Fixed in |
|---|----------|----------|
| 9 | Growth mandate | `workspace/mandate.md`, `README.md`, `agents/desk-lead.md`, `agents/research.md` |
| 10 | crypto-asymmetric-research | `skills/crypto-asymmetric-research/SKILL.md`, `agents/research.md`, `agents/critic.md` |
| 11 | Settle gates | `skills/sherwood-ops`, `routines/README.md` |
| 12 | rebalanceDelta | `skills/sherwood-ops`, `agents/desk-lead.md` |
| 13 | StalePrice on settle | `skills/sherwood-ops` troubleshooting |
| 14 | Roster GitHub push | `docs/SETUP.md`, `docs/HOSTING.md` |
| 15 | Morpho/CL cookbook | `workspace/ops/morpho-cl-cookbook.md` + `skills/sherwood-ops` (S1 IDs found / manual factory; S3 still blocked — not v4 equity LP) |
| 16 | Lead → COO template ping | `agents/desk-lead.md` |
