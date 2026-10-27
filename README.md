# Sherwood Fund Desk (Grok Bot template)

One Grok bot that runs one Sherwood fund: it sets up the vault, drafts proposals from the Sherwood data feeds and your own analysis, and watches them until they settle. **It never signs a tx, proposes, votes, settles or spends WOOD without your yes.** The one exception: it pays for research (x402) on its own within the daily budget you set.

For: the first Sherwood cohort (approved waitlist wallets) and agents that skip the waitlist by paying the factory's WOOD creation fee.

**Status: v1, live on Robinhood Chain mainnet (4663).** Mainnet values come from the Sherwood skill and CLI.

## Source of truth
| What | Where |
|---|---|
| CLI | `npm i -g @sherwoodagent/cli@latest` (always latest, never pinned) |
| MCP (optional, alternative to the CLI) | `https://mcp.sherwood.sh/mcp`, no install, [docs](https://docs.sherwood.sh/api/mcp) |
| Skill | https://github.com/sherwoodagent/skill (`SKILL.md`) |
| Docs | https://docs.sherwood.sh |

This repo only adds **desk policy and flow**. Commands, flags, addresses and errors live in the skill and CLI. If they disagree with this repo, the skill wins.

## Layout
```
BOT.md                       bot instructions (paste into the bot)
skills/fund-setup/SKILL.md   zero → vault (waitlist or WOOD fee), caps seeding
skills/propose/SKILL.md      /sources (optional) → stock-ticker check → draft → self-critique → bond + 5% → your yes
skills/watch/SKILL.md        lifecycle until Settled, weekend window
skills/research-byok/SKILL.md  research: Messari default (direct x402, MetaMask signs), Nansen fallback, optional Bankless / X
routines.md                  desk-pulse + proposal-watch
workspace/                   empty schemas the bot fills in (fund.json.example)
```

## Steps to a first proposal
The agent wallet is sign-only (MetaMask Agent Wallet first, Privy second): every write is built with `sherwood --calldata-only …`, shown to you, then signed and broadcast in order after your yes.
1. Add the template → the bot asks: waitlist or WOOD fee? MetaMask or Privy?
2. Install the latest CLI **or** connect the Sherwood MCP server instead (`https://mcp.sherwood.sh/mcp`, no npm/CLI install; `fund-setup` step 2b). Set up the agent wallet on Robinhood Chain (4663); fund it with ETH for gas, USDG and WOOD.
3. Mint the ERC-8004 identity.
4. Prepare the owner stake (`guardian prepare-owner-stake`), before the vault.
5. `vault create` (agent id + waitlist sponsorship **or** WOOD creation fee) → `vault info` → `fund.json`.
6. Register the creator wallet as the vault's agent (`vault add`).
7. Caps: none set by this desk. The protocol and vault caps apply; the owner can set vault caps if they want tighter ones. Leave the tier-2 cap at its default and ignore the `--bps 200` older CLIs print.
8. Optional: `sherwood sources login` (cohort-only, experimental; without it the bot reads Chainlink feeds directly and skips the liquidity and Morpho picks. Two-step; the CLI refreshes the 1-hour token for 7 days, so re-login weekly). See [sources commands](https://docs.sherwood.sh/cli/sources-commands).
9. Optional: join the cohort chat (`chat --join-cohort`; needs the CLI or the XMTP deps, even on the MCP path). See [XMTP](https://docs.sherwood.sh/reference/integrations/xmtp).
10. Starter proposal: a Portfolio stock basket (best pool per leg) or MorphoSupply (`--market-id recommended`), 30 days, shown to you before signing.
11. Turn on the proposal-watch routine.

Research budget: setup asks you for a daily USD research budget (`research.daily_budget_usd` in `fund.json`). The bot, routines included, pays Messari x402 calls on its own while today's spend stays within it (tracked in `workspace/research-spend.json`, reset daily); beyond it, it asks you per call.

Before every proposal the bot checks protocol price-feed freshness; if a feed is stale it skips and tells you. It also refuses any token that claims an official Robinhood stock ticker but isn't at that ticker's official address (an impersonator). Other tokens aren't blocked by this; if Robinhood's list is down it uses a local cache. It approves the WOOD proposer bond at the quote + 5% (capped) so a small price move doesn't revert the propose. After a cancel or settle it waits the governor's 1-hour cooldown before proposing again, and it dry-runs every settle (`sherwood proposal settle --dry-run`, needs CLI 0.97.0 or later; a plain `eth_call` on the MCP path) before signing; if it would revert, it doesn't send. After any killed proposal (expired included) it needs a fresh yes from you before proposing again.

Attestations (on by default, opt out with `"attest": false` in `workspace/fund.json`): the bot attests each thesis and its proposal link, and each abort with a reason code, on Robinhood Chain EAS, for a public track record depositors can trust. Small gas each, paid by the agent wallet; a failed attestation never blocks a proposal. See [EAS](https://docs.sherwood.sh/reference/integrations/eas).

## Add it as a Grok template
Templates carry instructions, skills, routines and memories. They do **not** carry repo files or scripts, so build the bot from this repo, then share it.
1. Create a **fresh** Grok bot named "Sherwood Fund Desk". Don't reuse a bot that has chat history.
2. Paste `BOT.md` as its instructions. Add the four `skills/*/SKILL.md` as skills, with `fund-setup` as getting started.
3. Create the two routines from `routines.md`, **disabled**. The bot turns them on after setup.
4. Add one memory: "Desk policy and skills: github.com/sherwoodagent/grokbot-fund-template. Source of truth: Sherwood skill + CLI. Setup gate first."
5. Ask the bot: "Prepare this bot to be shared as a template: list every dependency, remove anything personal or secret, show me what will be included."
6. Settings → **Share as Template** → View details → check every section → Publish (Public) → Copy link.
7. Have someone else install it with no help. Anything they had to guess goes back into `fund-setup`.
After you change the bot, use **Update template**.

## Known limits
- Access: a wallet sponsored through the Sherwood waitlist creates a vault for free (once). Any other wallet pays the factory's creation fee in WOOD (1,000,000 WOOD at launch; `vault create` reads the live amount and approves exactly the fee) and skips the waitlist.
- LaunchPad and Lighter are live on 4663 but unaudited; a proposal using either needs full guardian coverage booked to execute.
- Lighter: vault strategies trade on Robinhood's Lighter venue (`api.rh.lighter.xyz`), not main Lighter. Market ids differ (QQQ is 25 there vs 129 on main), so read ids from `sherwood lighter markets`, never from main Lighter docs.
- `/sources` is cohort-only and experimental (approved waitlist wallets). Without it the bot reads Chainlink feeds directly and skips the liquidity and Morpho picks.
- MCP path: no `/sources`, no cohort chat (needs the CLI or the XMTP deps), and strategy `initData` plus execute/settle calls are caller-built; if the bot can't build and simulate them, it uses the CLI for that proposal.

MIT. Not investment advice.
