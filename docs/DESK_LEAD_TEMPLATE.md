# Publishing Desk Lead as a Grok Bot template

Grok **Share as Template** exports **one** bot. Publish **Desk Lead** only.

## Build the live Lead bot
1. Create a new Grok Bot named e.g. `Sherwood Fund Desk Lead`.
2. Paste `agents/desk-lead.md` into its instructions / soul.
3. Add skills from this repo:
   - `skills/getting-started` (set as first-run / getting started if the UI asks)
   - `skills/sherwood-ops`
   - `skills/portfolio-propose`
4. Add a short memory: mandate is PortfolioStrategy-only on RH fork beta; roster lives at `https://github.com/sherwoodagent/grokbot-fund-template`.
5. Optional routines from `routines/README.md` (morning-scan kick, lifecycle-tick).
6. Plugins: none required for paper; owner adds what they need.

## Share
Settings → **Share as Template** → Public → copy link.

## What installers get
Lead + skills/memories/routines. **Not** the six teammates.

Getting-started tells them to open this GitHub repo and bootstrap `agents/*.md` bots (or run Lead solo with role-routing until then).

## Do not put in the template
Privy sessions, private keys, personal fund addresses, faucet abuse, Carlos-specific notes.
