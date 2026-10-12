---
name: fund-setup
description: Use on first run, or whenever workspace/fund.json has no vault. Takes the owner from zero to a funded, capped Sherwood vault with /sources access.
---
# Fund setup

Defer to the Sherwood skill (`Phase 1: Setup`, `Phase 2: Create or Join Vault`) for every flag, address and error. This skill only fixes order and desk choices. Do one step per turn, record it in `workspace/fund.json`, and resume at the first unfinished step.

**Every write is two-step.** Run it with the root flag `sherwood --calldata-only …`. The CLI prints `{ txs: [{to,data,value,chainId}], preconditions, description, note }`. Show that to the owner, get a yes, then sign and broadcast each tx **in order** with the agent wallet (skill → `Phase 1: Setup` has the MetaMask and Privy recipes), waiting for each receipt before the next. Never skip or reorder a tx.

## 1. Ask (one message)
- **Access path:** approved cohort waitlist wallet, or paying the factory's WOOD creation fee?
- **Signer:** MetaMask Agent Wallet (`mm`, recommended) or Privy agent wallet. Both are sign-only: the CLI builds calldata, the wallet signs and broadcasts.
- Fund name, subdomain, and whether deposits are open.

## 2. Install + wallet
`npm i -g @sherwoodagent/cli@0.93.0` (or `@latest` once newer), then `sherwood --version` must be ≥ 0.93.0. Set up the wallet per the skill (`Phase 1: Setup`). MetaMask: `npm i -g @metamask/agent-wallet@latest`, `mm login` (you approve a device code), then check that `mm chains list` shows 4663 after login; if it does not, use Privy. Tell the owner what to fund it with: ETH for gas on 4663, USDG, and WOOD for the owner stake (plus the creation fee if on that path) and for proposer bonds. USDC on Base only if the owner turns on `research-byok`.

## 3. Identity (needs a yes)
`sherwood --calldata-only identity mint --name <fund name>` → sign/broadcast. Record `agentId` from the CLI output (`sherwood identity load --id <id>` if needed). Never invent one.

## 4. Owner stake (needs a yes; spends WOOD)
`sherwood --calldata-only guardian prepare-owner-stake 10000` → sign/broadcast both txs (WOOD approve, then prepare). Do this **before** `vault create`; without it create reverts with `PreparedStakeNotFound`.

## 5. Vault (needs a yes)
`sherwood --calldata-only vault create -y --name <name> --subdomain <sub> --agent-id <agentId> --asset USDG [--open-deposits] --creator <wallet>` → sign/broadcast. The CLI checks three things first: owner stake prepared, `--agent-id` owned by this wallet, and **either** waitlist sponsorship **or** the factory creation fee in WOOD (amount read from the factory; don't quote a number from memory).
Then `sherwood vault info <subdomain>` and write `vault`, `asset`, `owner`, `agent`, `agentId`, `chainId` into `fund.json` from that output (schema: `workspace/fund.json.example`; keep `attest` and `attestLicense` as the owner set them).

## 6. Register the creator as agent (needs a yes)
`sherwood --calldata-only vault add --vault <vault> --wallet <creator wallet> --agent-id <agentId>` → sign/broadcast. Without it, keyless `strategy propose` fails with `not a registered agent`.

## 7. Caps (needs a yes)
Owner-only, no open proposal needed. Three calls, each two-step:
```
sherwood --calldata-only governor set-tier2-call-cap --vault <vault> --bps 200
sherwood --calldata-only governor set-max-capital --vault <vault> --bps 8000
sherwood --calldata-only vault set-min-buffer --vault <vault> --bps 500
```
Tell the owner the 200 bps tier-2 call cap is conservative (it may refuse a larger Portfolio basket) and that Sherwood is confirming the recommended value. Record the caps in `fund.json`.

**Agent version (optional, on by default; needs a yes).** Attestations give your fund a public track record depositors can trust over time; they are never required (set `"attest": false` in `fund.json` to opt out). If `attest` is true, write `workspace/model-config.json` (model, desk template commit, skill and CLI versions) and run `sherwood --calldata-only attest version --agent-id <agentId> --agent <agent wallet> --model-config workspace/model-config.json --license <attestLicense>` once → sign/broadcast. Run it again whenever the model or that config changes. If it fails, log it and continue setup (skill → `Phase 6: Monitor & Communicate` → `Public track record`).

## 8. Data access
Two-step login (skill → `Phase 1: Setup`): `sherwood sources login --address <wallet>` prints a message → sign it with personal_sign (MetaMask: `mm wallet sign-message --message <text> --chain-id 4663 --wait`; Privy: `personal_sign` via its rpc) → `sherwood sources login --signature 0x…`. The CLI refreshes the 1-hour token itself for 7 days, so routines re-login weekly, not every run: `sherwood sources status` shows `refresh_expires_at` and `auto_refresh` (false = re-login now). If no one can sign unattended, ask the owner to sign the weekly message.
- **403** (at login, or on the CLI's refresh): this wallet is not approved, or no longer approved. Tell the owner and stop. Don't swap wallets and don't retry in a loop.
- **401** (after the CLI's own refresh attempt): the login expired or is missing. Re-login with the two-step above.
- Waitlist path: works once the owner's wallet is approved.
- WOOD-fee path: Access after paying the creation fee is granted once the indexer ships; until then use an approved waitlist wallet.

## 9. Cohort chat (optional; ask the owner)
1. `sherwood chat --join-cohort --address <wallet> --json > /tmp/join.log 2>&1 &` (step 1 waits in the background and writes the XMTP text to sign).
2. Sign that text with the wallet (MetaMask: `mm wallet sign-message --message <text> --chain-id 4663 --wait`), then `sherwood chat --join-cohort --signature 0x…`.
3. The cohort bot processes joins about every 2 minutes. Check once later with `sherwood chat --cohort log`. Don't loop (rate limit 10/min). Never delete `xmtpDbKey` from `~/.sherwood/config.json`; that loses the chat identity.

## 10. Done
Post the checklist with every box ticked, offer a starter proposal (`propose` skill), and enable `desk-pulse`.
