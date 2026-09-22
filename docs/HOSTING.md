# Where this lives / how we publish

## Source of truth
GitHub: https://github.com/sherwoodagent/grokbot-fund-template

## Publish path (hybrid)
1. **This repo** — agents, personas, skills, workspace contract, docs
2. **Grok Share as Template** — Desk Lead only (see `DESK_LEAD_TEMPLATE.md`)
3. Lead’s getting-started skill helps clone teammates from `agents/*.md`

Secrets never ship: Privy session, keys, faucet abuse, personal wallets.

## Roster push (GitHub)

Most roster bots cannot push to this repo (no Contents write on MCP; `gh` often logged out). Publish recipe: **Lead commits from the shared box** or **`gh` auth on Ops/Lead only** — details in `docs/SETUP.md` § Roster bots cannot push GitHub. PROCESS/skills/strategies land via branch + PR unless COO is doing a known direct-main ops brief.
