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
   > PortfolioStrategy-only on RH fork beta (chain 9994663). Linear installer: wallet → identity (ERC-8004 on RH mainnet; optional on fork, required on prod) → create fund → fund.json → roster → paper. Refuse skip-ahead. Roster + prompts: https://github.com/sherwoodagent/grokbot-fund-template
5. Optional routines from `routines/README.md` (morning-scan kick, lifecycle-tick).
6. Plugins: none required for paper; owner adds what they need.

## Share
Settings → **Share as Template** → Public → copy link.

## What installers get
Lead + skills/memories/routines. **Not** the six teammates.

`getting-started` runs a sticky checklist and refuses paper/live until wallet + identity (or recorded skip) + vault + fund.json (+ roster or one-shot waiver) are done. Teammates are created from `agents/*.md` in step 4.

## Do not put in the template
Privy sessions, private keys, personal fund addresses, faucet abuse, Carlos-specific notes.
