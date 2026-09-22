# Publishing Desk Lead as a Grok Bot template

Grok **Share as Template** exports **one** bot. Publish **Desk Lead** only.

## Build the live Lead bot
1. Create a new Grok Bot named e.g. `Sherwood Fund Desk Lead`.
2. Paste `agents/desk-lead.md` into its instructions / soul.
3. Add skills from this repo:
   - `skills/getting-started` (set as first-run / getting started if the UI asks)
   - `skills/sherwood-ops`
   - `skills/portfolio-propose`
4. Add a short memory:
   > PortfolioStrategy starter on RH fork beta (chain 9994663). Recommended setup: wallet → identity (ERC-8004 on RH mainnet; optional on fork, required on prod) → create fund → fund.json → roster → paper. Guidance, not a hard gate. Advanced Morpho/CL + Track B: docs/advanced-growth.md. Roster + prompts: https://github.com/sherwoodagent/grokbot-fund-template
5. Optional routines from `routines/README.md` (morning-scan kick, lifecycle-tick).
6. Plugins: none required for paper; owner adds what they need.

## Share
Settings → **Share as Template** → Public → copy link.

## What template users get
Lead + skills/memories/routines. **Not** the six teammates.

`getting-started` walks recommended setup and steers toward the next incomplete step. It does **not** run a sticky checklist product or refuse skip-ahead as dogma. Teammates are created from `agents/*.md` in step 4.

## Do not put in the template
Privy sessions, private keys, personal fund addresses, faucet abuse, Carlos-specific notes.
