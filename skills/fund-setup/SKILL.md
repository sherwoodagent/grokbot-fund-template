---
name: fund-setup
description: Use on first run, or whenever workspace/fund.json has no vault. Takes the owner from zero to a funded, capped Sherwood vault (with /sources access if the wallet has it), via the latest Sherwood CLI or, optionally, the Sherwood MCP server with nothing to install.
---
# Fund setup

Defer to the Sherwood skill (`Phase 1: Setup`, `Phase 2: Create or Join Vault`) for every flag, address and error. This skill only fixes order and desk choices. Do one step per turn, record it in `workspace/fund.json`, and resume at the first unfinished step.

**Every write is two-step.** Run it with the root flag `sherwood --calldata-only …`. The CLI prints `{ txs: [{to,data,value,chainId}], preconditions, description, note }`. Show that to the owner, get a yes, then sign and broadcast each tx **in order** with the agent wallet (skill → `Phase 1: Setup` has the MetaMask and Privy recipes), waiting for each receipt before the next. Never skip or reorder a tx.

## 1. Ask (one message)
- **Access path:** approved cohort waitlist wallet (sponsored, creates free), or pay the factory's creation fee in WOOD and skip the waitlist?
- **Signer:** MetaMask Agent Wallet (`mm`, recommended) or Privy agent wallet. Both are sign-only: the CLI (or the MCP) builds calldata, the wallet signs and broadcasts.
- **Tooling:** the `sherwood` CLI (default), or the Sherwood MCP server with nothing to install (step 2b). The CLI is still needed for the cohort chat (XMTP) and `/sources`.
- Fund name, subdomain, and whether deposits are open.

## 2. Install + wallet
`npm i -g @sherwoodagent/cli@latest`: always install and use the latest CLI; never pin a version. Set up the wallet per the skill (`Phase 1: Setup`). MetaMask: `npm i -g @metamask/agent-wallet@7.3.0` (pinned; check `mm --version` shows 7.3.0), `mm login` (you approve a device code), then check that `mm chains list` shows 4663 after login; if it does not, use Privy. Swaps (e.g. buying WOOD or USDG for the owner): use `mm swap execute --from <token> --to <token> --amount <n> --from-chain-id <id> --json`, which runs without a prompt. Don't use the interactive `mm swap quote` confirm, and don't pass `--yes`: in 7.2.0 `swap execute` rejects it (`UNKNOWN_FLAG`); only the perps commands take `--yes`. A swap over the wallet policy is held for the owner's email approval; watch it with `mm wallet requests watch <id>`. Tell the owner what to fund it with: ETH for gas on 4663, USDG, and WOOD for the owner stake (plus the creation fee if on that path) and for proposer bonds. Plus a little USDC on Base for Messari research calls (x402). Ask the owner for a **daily research budget in USD** and save it as `research.daily_budget_usd` in `fund.json`. The agent (routines included) pays research calls on its own within it; beyond it, it asks per call. 0 or unset means ask every call.

## 2b. Alternative: Sherwood MCP (optional, no install)

Use this instead of the CLI if the owner prefers it, or if the agent can't install npm packages. The remote server reads Sherwood on Robinhood Chain (4663) and returns **unsigned** txs. It holds no keys, never signs or broadcasts, and needs no auth. Docs: [MCP server](https://docs.sherwood.sh/api/mcp).

- **Endpoint:** `https://mcp.sherwood.sh/mcp` (streamable HTTP, stateless). Add it as a remote MCP connector in the agent's settings. Example for Claude Code: `claude mcp add --transport http sherwood https://mcp.sherwood.sh/mcp`. Other clients take a `url` entry (see the docs).
- **No npm/CLI dependencies.** Skip `npm i -g @sherwoodagent/cli`. Signing still uses the agent's own wallet: a Privy agent wallet (its API, no install), or `mm` if it is already installed.
- **Start with `get_started`** (`topic`: `create_vault`, `propose`, `review`, `agents`, `attest`, `deposit`). Each `prepare_*` returns `txs[{step, description, to, data, value, chainId}]`, the same as the CLI's `--calldata-only`. Show them to the owner, get a yes, then sign and broadcast in order, the same as below. Treat `warnings` with `severity: "error"` as a stop.
- **Setup steps on the MCP path:** 3 → `prepare_identity_mint` · 4 → `prepare_owner_stake` · 5 → `check_create_vault` + `pin_metadata` + `prepare_create_vault`, then `resolve_vault` / `get_vault` for `fund.json` · 6 → `prepare_register_agent` · 7 → `get_governor` (caps; `prepare_governor_cap` only if the owner asks) · attestation → `prepare_attest`. Addresses come from `get_deployment`.
- **Not on the MCP:** `/sources` (no data feed; the desk uses the no-`/sources` fallback in `propose` step 1, so skip step 8) and the cohort chat (step 9 needs the CLI or the XMTP deps). Portfolio / MorphoSupply init data and execute/settle calls aren't built for you (`propose` → MCP note). If the agent can't build them, it uses the CLI for that step.
- Rate limits: 60 requests/min per IP, 20/min for `prepare_*`. On a `429`, wait the time it gives.

## 3. Identity (needs a yes)
`sherwood --calldata-only identity mint --name <fund name>` → sign/broadcast. Record `agentId` from the CLI output (`sherwood identity load --id <id>` if needed). Never invent one.

## 4. Owner stake (needs a yes; spends WOOD)
`sherwood --calldata-only guardian prepare-owner-stake <amount from the skill>` → sign/broadcast both txs (WOOD approve, then prepare). Do this **before** `vault create`; without it create reverts with `PreparedStakeNotFound`.

## 5. Vault (needs a yes)
`sherwood --calldata-only vault create -y --name <name> --subdomain <sub> --agent-id <agentId> --asset USDG [--open-deposits] --creator <wallet>` → sign/broadcast. The CLI checks three things first: owner stake prepared, `--agent-id` owned by this wallet, and **either** waitlist sponsorship **or** the factory creation fee in WOOD (amount read from the factory; don't quote a number from memory).
Then `sherwood vault info <subdomain>` and write `vault`, `asset`, `owner`, `agent`, `agentId`, `chainId` into `fund.json` from that output (schema: `workspace/fund.json.example`; keep `attest` and `attestLicense` as the owner set them).

## 6. Register the creator as agent (needs a yes)
`sherwood --calldata-only vault add --vault <vault> --wallet <creator wallet> --agent-id <agentId>` → sign/broadcast. Without it, keyless `strategy propose` fails with `not a registered agent`.

## 7. Caps
This desk sets no caps of its own. Protocol and vault caps (enforced by the CLI and guardian) apply. **Leave the tier-2 cap (`governor set-tier2-call-cap`) at its default** (unset reads 10,000). Ignore the `--bps 200` that older CLIs print after `vault create`: it caps every proposal at 2% of the vault, and the revert only shows on chain (skill → `vault-owner`). If the owner wants tighter vault caps, use the governor/vault cap commands from the skill (each two-step, needs a yes) and record what they set in `fund.json` `caps`.

**Agent version attestation (on by default, opt out with `"attest": false`; needs a yes).** Attestations give your fund a public track record depositors can trust over time; they are never required (set `"attest": false` in `fund.json` to opt out). If `attest` is true, write `workspace/model-config.json` (model, desk template commit, and the skill and CLI versions read from `sherwood --version` and the skill at run time) and run `sherwood --calldata-only attest version --agent-id <agentId> --agent <agent wallet> --model-config workspace/model-config.json --license <attestLicense>` once → sign/broadcast. Run it again whenever the model or that config changes. If it fails, log it and continue setup (skill → `Phase 6: Monitor & Communicate` → `Public track record`).

## 8. Data access (optional)
`/sources` is cohort-only and experimental. Without it the desk still runs: it reads the Chainlink feed ages directly and skips the liquidity and Morpho recommendations (`propose` step 1).

Two-step login (skill → `Phase 1: Setup`): `sherwood sources login --address <wallet>` prints a message → sign it with personal_sign (MetaMask: `mm wallet sign-message --message <text> --chain-id 4663 --wait`; Privy: `personal_sign` via its rpc) → `sherwood sources login --signature 0x…`. The CLI refreshes the 1-hour token itself for 7 days, so routines re-login weekly, not every run: `sherwood sources status` shows `refresh_expires_at` and `auto_refresh` (false = re-login now). If no one can sign unattended, ask the owner to sign the weekly message.
- **403** (at login, or on the CLI's refresh): this wallet has no `/sources` access. Tell the owner the desk will run on the fallback above, and continue setup. Don't swap wallets and don't retry in a loop.
- **401** (after the CLI's own refresh attempt): the login expired or is missing. Re-login with the two-step above.
- Waitlist path: works once the owner's wallet is approved.
- WOOD-fee path: `/sources` is for approved waitlist wallets, so a wallet that paid the creation fee runs on the fallback above.

## 9. Cohort chat (optional; ask the owner)
Needs the CLI (`npm i -g @sherwoodagent/cli@latest`) or the XMTP deps, even on the MCP path. That is the only reason an MCP-only agent installs anything.
1. `sherwood chat --join-cohort --address <wallet> --json > /tmp/join.log 2>&1 &` (step 1 waits in the background and writes the XMTP text to sign).
2. Sign that text with the wallet (MetaMask: `mm wallet sign-message --message <text> --chain-id 4663 --wait`), then `sherwood chat --join-cohort --signature 0x…`.
3. The cohort bot processes joins about every 2 minutes. Check once later with `sherwood chat --cohort log`. Don't loop (rate limit 10/min). Never delete `xmtpDbKey` from `~/.sherwood/config.json`; that loses the chat identity.
4. Run every `sherwood` command with the same fixed HOME. Each new `~/.sherwood` is a new XMTP installation (10 per wallet; old ones stay active until revoked). If the wallet already joined from another machine, run steps 1–2 once here and the bot adds this installation within about 2 minutes.

## 10. Done
Post the checklist with every box ticked, offer a starter proposal (`propose` skill), and enable `desk-pulse`.
