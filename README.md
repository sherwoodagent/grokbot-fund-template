# Sherwood Fund Desk (Grok Bot template)

Multi-agent **agentic fund desk** for Sherwood’s incentivized beta on the Robinhood-chain fork.

**Repo:** https://github.com/sherwoodagent/grokbot-fund-template

Recipe: install Desk Lead template → **linear installer** (wallet → identity → create fund → fund.json → roster → paper) → optional live.

## How sharing works

| Piece | Mechanism |
|-------|-----------|
| Desk Lead | Grok **Share as Template** — see `docs/DESK_LEAD_TEMPLATE.md` |
| Scanner / Research / Critic / PM / Risk / Ops | `agents/` — installer step 4 (not auto-cloned) |
| Personas | `personas/` lenses (tags, not LARPing) |
| Workspace | `workspace/` shared artifacts |

## Mandate (beta) — growth edition

See `workspace/mandate.md`.

- **Strategies:** PortfolioStrategy (primary) + MorphoSupply + ConcentratedLiquidity (satellites; live only with documented market/pool ids)
- **Book:** core 60–100% equities / satellite 0–40%
- **Tracks:** A live equities · B crypto paper → honest RH equity proxies only
- No opaque calldata / memecoins / unlisted venues
- One live strategy at a time

## Installer order (mandatory)

See `docs/SETUP.md`. Desk Lead refuses skip-ahead.

1. Wallet — Privy (`pnpm dlx`) + faucet  
2. Identity — ERC-8004 mint on Robinhood **mainnet** (4663); optional on the fork beta, required on production, highly recommended  
3. Create fund — then write `workspace/fund.json`  
4. Roster — six bots from `agents/*.md` (or one-shot waiver)  
5. Paper — Scanner → Research → Critic → PM → Risk  
6. Live — Risk APPROVE + known RH basis + Privy sign→raw  

## Credits

Patterns adapted from RohOnChain research-desk swarm, galleonlabs/hypergrok-trading-desk,
TauricResearch/TradingAgents, and virattt/ai-hedge-fund–style persona lenses
(stylized educational approximations — not the real people).
