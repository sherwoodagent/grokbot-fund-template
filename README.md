# Sherwood Fund Desk (Grok Bot template)

Multi-agent **agentic fund desk** for Sherwood’s incentivized beta on the Robinhood-chain fork.

**Repo:** https://github.com/sherwoodagent/grokbot-fund-template

Recipe, not a meal: install Desk Lead template → connect Privy + Sherwood skill → paper loop → bootstrap teammates from this repo → optional live propose.

## How sharing works

| Piece | Mechanism |
|-------|-----------|
| Desk Lead | Grok **Share as Template** (one-click) — see `docs/DESK_LEAD_TEMPLATE.md` |
| Scanner / Research / Critic / PM / Risk / Ops | Prompts in `agents/` — Lead’s getting-started skill bootstraps them |
| Personas | `personas/` lenses (not separate bots) |
| Workspace contract | `workspace/` shared files |

## Mandate (beta)

- **PortfolioStrategy only** (tokenized RH equities basket)
- No opaque calldata / memecoin / DEX as primary
- One live strategy at a time; governance clock via routines

## Desk roster

| Bot | Role |
|-----|------|
| Desk Lead | Orchestrator / maker→checker |
| Scanner | Watchlist movers |
| Research | Bull thesis (Lynch/Buffett lens) |
| Critic | Bear case (Burry lens) |
| PM | Weights + duration (Druckenmiller-lite) |
| Risk | Hard veto (Munger checklist) |
| Ops | Privy sign + raw broadcast + lifecycle |

## Quick start

1. Owner installs Lead from your Grok template link (or clones this repo and pastes `agents/desk-lead.md`).
2. Lead runs `skills/getting-started` (Q1–Q4).
3. Paper: scan → research ↔ critic → draft → risk.
4. Live: only after Risk APPROVE + Sherwood skill incentivized-beta wallet path.

## Credits

Patterns adapted from RohOnChain research-desk swarm, galleonlabs/hypergrok-trading-desk,
TauricResearch/TradingAgents, and virattt/ai-hedge-fund–style persona lenses
(stylized educational approximations — not the real people).
