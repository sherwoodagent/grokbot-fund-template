# Sherwood Fund Desk (Grok Bot template)

One Grok bot that runs one Sherwood fund: it sets up the vault, drafts proposals from the Sherwood data feeds and your own analysis, and watches them until they settle. **It never signs, proposes, votes, settles or spends WOOD without your yes.**

For: the first Sherwood cohort (approved waitlist wallets) and agents that skip the waitlist by paying the factory's WOOD creation fee.

**Status: v1 rebuild, not published.** Mainnet (Robinhood Chain 4663) values come from the Sherwood skill. Anything still unpublished is marked `{{...}}`. See [Blocked on mainnet](#blocked-on-mainnet).

## Source of truth
| What | Where | Pinned |
|---|---|---|
| CLI | `npm i -g @sherwoodagent/cli@latest` | ≥ 0.91.2 (`sherwood sources` + `--market-id recommended` land with sherwood PR #647, pending) |
| Skill | https://github.com/sherwoodagent/skill (`SKILL.md`) | ≥ 0.24.2 (`sources` section in skill PR #81, pending) |
| Docs | https://docs.sherwood.sh | optional |

This repo only adds **desk policy and flow**. Commands, flags, addresses and errors live in the skill and CLI. If they disagree with this repo, the skill wins.

## Layout
```
BOT.md                       bot instructions (paste into the bot)
skills/fund-setup/SKILL.md   zero → vault (waitlist or WOOD fee), caps seeding
skills/propose/SKILL.md      /sources → draft → self-critique → your yes
skills/watch/SKILL.md        lifecycle until Settled, weekend window
skills/research-byok/SKILL.md  optional: your own Bankless / X / Messari / Nansen
routines.md                  desk-pulse + proposal-watch
workspace/                   empty schemas the bot fills in
```

## Seven steps to a first proposal
1. Add the template → the bot asks: waitlist or WOOD fee? Privy or your own signer?
2. Install the CLI and create the agent wallet; fund it with gas, USDG and WOOD.
3. Mint the ERC-8004 identity on 4663.
4. `vault create` (owner bond + agent id + waitlist sponsorship **or** WOOD creation fee) → `fund.json` → seed caps.
5. `sherwood sources login`.
6. Starter proposal: a Portfolio stock basket (best pool per leg) or MorphoSupply (`--market-id recommended`), 30 days, shown to you before signing.
7. Turn on the proposal-watch routine.

## Add it as a Grok template
Templates carry instructions, skills, routines and memories. They do **not** carry repo files or scripts, so build the bot from this repo, then share it.
1. Create a **fresh** Grok bot named "Sherwood Fund Desk". Don't reuse a bot that has chat history.
2. Paste `BOT.md` as its instructions. Add the four `skills/*/SKILL.md` as skills, with `fund-setup` as getting started.
3. Create the two routines from `routines.md`, **disabled**. The bot turns them on after setup.
4. Add one memory: "Desk policy and skills: github.com/sherwoodagent/grokbot-fund-template. Source of truth: Sherwood skill + CLI. Setup gate first."
5. Ask the bot: "Prepare this bot to be shared as a template: list every dependency, remove anything personal or secret, show me what will be included."
6. Settings → **Share as Template** → View details → check every section → Publish (Team-only until v1 is done, then Public) → Copy link.
7. Have someone else install it with no help. Anything they had to guess goes back into `fund-setup`.
After you change the bot, use **Update template**.

## Blocked on mainnet
- 4663 factory, vault and strategy addresses, and whether MorphoSupply and Portfolio are deployed there (`sherwood strategy list`).
- CLI verbs for the per-syndicate caps (`{{CAPS_CLI_COMMAND}}`).
- `sherwood sources` live (PR #647 deploy + waitlist DB URL) and the WOOD-fee access flag (indexer, not built).
- Provenance output (#646) command.

MIT. Not investment advice.
