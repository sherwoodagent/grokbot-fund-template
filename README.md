# Sherwood Fund Desk (Grok Bot template)

Multi-agent **agentic fund desk** for Sherwood’s incentivized beta on the Robinhood-chain fork.

**Repo:** https://github.com/sherwoodagent/grokbot-fund-template

Recipe: install Desk Lead template → recommended setup (wallet → identity → create fund → fund.json → roster → paper) → optional live. Setup is **guidance**, not a hard installer that blocks progress.

## How sharing works

| Piece | Mechanism |
|-------|-----------|
| Desk Lead | Grok **Share as Template** — see `docs/DESK_LEAD_TEMPLATE.md` |
| Scanner / Research / Critic / PM / Risk / Ops | `agents/` — setup step 4 (not auto-cloned) |
| Personas | `personas/` lenses (tags, not LARPing) |
| Workspace | `workspace/` shared artifacts |

## What you can do (strategies)

See `workspace/strategies.md`.

- **Starter (default):** PortfolioStrategy only · paper-first · one live book at a time
- **Growth / advanced (opt-in):** MorphoSupply + ConcentratedLiquidity satellites + dual Track A/B crypto research — `docs/advanced-growth.md`
- No opaque calldata / memecoins / unlisted venues
- One live strategy at a time

## Recommended setup order

See `docs/SETUP.md`. Sensible order — document progress, don’t gate the product on a sticky checklist.

1. Wallet — Privy (`pnpm dlx`) + faucet
2. Identity — ERC-8004 mint on Robinhood **mainnet** (4663); optional on the fork beta, required on production, highly recommended
3. Create fund — then write `workspace/fund.json`
4. Roster — six bots from `agents/*.md` (or one-shot waiver)
5. Paper — Scanner → Research → Critic → PM → Risk
6. Live — Risk APPROVE + known RH basis + Privy sign→raw

Ops pitfalls (Privy sign→raw, Critic waits, Settled watch, Morpho/CL honesty, settle/rebalanceDelta/StalePrice): `docs/FRICTION.md`.

## Credits

Patterns adapted from RohOnChain research-desk swarm, galleonlabs/hypergrok-trading-desk,
TauricResearch/TradingAgents, and virattt/ai-hedge-fund–style persona lenses
(stylized educational approximations — not the real people).
