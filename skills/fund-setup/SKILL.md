---
name: fund-setup
description: Use on first run, or whenever workspace/fund.json has no vault. Takes the owner from zero to a funded, capped Sherwood vault with /sources access.
---
# Fund setup

Defer to the Sherwood skill (Setup and Create phases) for every command. This skill only fixes order and desk choices. Do one step per turn, record it in `workspace/fund.json`, and resume at the first unfinished step.

## 1. Ask (one message)
- **Access path:** approved cohort waitlist wallet, or paying the factory's WOOD creation fee?
- **Signer:** Privy (recommended) or your own wallet?
- Fund name, subdomain, and whether deposits are open.

## 2. Install + wallet
`npm i -g @sherwoodagent/cli@latest`, then `sherwood --version` must be ≥ 0.91.2. Make sure no custom RPC env var points at the old fork. Create or attach the wallet per the skill. Tell the owner what to fund it with: gas, USDG, WOOD for the owner bond (plus the creation fee if on that path) and for proposer bonds.

## 3. Identity
Mint the ERC-8004 agent identity on 4663 per the skill. Record `agentId`. Never invent one.

## 4. Vault (needs a yes)
`vault create` needs three things, and the CLI checks them before sending:
1. owner bond prepared,
2. `--agent-id` owned by this wallet,
3. **either** waitlist sponsorship **or** the factory creation fee in WOOD (amount and token read from the factory; don't quote a number from memory).
Show the full command plus the simulation → yes → broadcast. Write `vault`, `asset`, `owner`, `agent`, `agentId`, `chainId` into `fund.json` from the CLI output, then verify through `sherwood vault info`.

**Seed caps right after (needs a yes):** `setTier2CallCapBps 200`, `setMaxCapitalBps 8000`, `setMinBufferBps 500`, via `{{CAPS_CLI_COMMAND}}`. If the skill doesn't publish a command yet, tell the owner it's pending and continue.

## 5. Data access
`sherwood sources login` (Privy and other external signers: the two-step `--address` → sign → `--signature` flow in the skill).
- Waitlist path: works once the owner's wallet is approved.
- WOOD-fee path: access is granted automatically after the fee or creation event is indexed (the flag can lag). If login says "not allowed", wait and retry later. Don't loop.

## 6. Done
Post the checklist with every box ticked, offer a starter proposal (`propose` skill), and enable `desk-pulse`.
