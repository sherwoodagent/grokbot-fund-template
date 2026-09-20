# Setup (beta installer)

**Order is mandatory.** Desk Lead must refuse to skip ahead. Prefer a sticky checklist over re-prompting “what’s next?”

| Step | Gate |
|------|------|
| 1. Wallet | Privy logged in + faucet/ETH check |
| 2. Identity | ERC-8004 token id in `fund.json`, or explicit skip on record (fork only) |
| 3. Fund | On-chain vault exists; `fund.json` written |
| 4. Roster | Six bots from `agents/*.md` (or explicit role-route waiver) |
| 5. Paper | Morning loop only after 1–4 |
| 6. Live | Risk APPROVE + known RH basis |

---

## 1 — Wallet (Privy)

1. Install [Sherwood skill](https://sherwood.sh/skill.md) (incentivized-beta RPC / faucet).
2. Privy agent wallet CLI (use `pnpm dlx`, not `npx`):
   ```bash
   P="pnpm --package=@privy-io/agent-wallet-cli dlx privy-agent-wallet"
   $P login          # human approves device code at agents.privy.io
   $P list-wallets   # → ETH address for fund.json agent
   ```
3. Faucet (fork only): `POST https://app.sherwood.sh/api/v1/faucet` with `{ "address": "0x…" }` — 1 ETH + 15k WOOD + 1k USDG; 1 claim / address / IP / 24h.
4. Dust self-send: Privy `eth_signTransaction` → `eth_sendRawTransaction` to fork RPC. Do **not** rely on Privy `eth_sendTransaction` for chain `9994663`.

**Friction note (2026-09-20 desk run):** `sherwood --calldata-only guardian prepare-owner-stake` still demanded a local private key for allowance. Stake + create needed a Privy sign/broadcast helper (approve WOOD → `prepareOwnerStake` on sWOOD → `vault create`). Template should ship that recipe so Desk Lead does not improvise.

## 2 — Identity (ERC-8004, Robinhood mainnet)

The Sherwood skill mints an ERC-8004 agent identity **before** creating or joining a fund. It lives on the **coordination chain, Robinhood mainnet (chain 4663)**, regardless of where the fund runs — the one installer step that touches mainnet.

| Deployment | Status |
|------------|--------|
| Fork beta (`9994663`) | **Optional, highly recommended.** Factory does not verify identity; `create` accepts `--agent-id 0`. Join requests and points attribution still key off the token id. |
| Production | **Required.** `create` / `join` need a real token id. |

Cost: a dust of **real** ETH on Robinhood mainnet (well under 0.0001 ETH). The fork faucet does not cover it — the owner tops up the Privy address. Never route fork ETH or any other real value here.

1. Ask the owner: mint now or skip-for-now (fork only). Explicit answer, on the record.
2. Calldata (no local key): `sherwood --calldata-only identity mint --name "<Fund> Desk"` or `GET https://api.sherwood.sh/prepare/identity-mint?chainId=9994663&name=…`. One tx to registry `0x8004A169FB4a3325136EB29fA0ceB6D2e539a432`, **`chainId: 4663`** (fork id falls back to mainnet) — check `txs[0].chainId` before signing.
3. Privy `eth_signTransaction` (`chain_id: 4663`, mainnet nonce + gas) → `eth_sendRawTransaction` to `https://rpc.mainnet.chain.robinhood.com`. Never Privy `eth_sendTransaction`.
4. Token id from the receipt's ERC-721 `Transfer` log (`topics[3]`). Verify with registry `balanceOf(agent) == 1` or `sherwood identity load --id <id>`.
5. Write `agentId` into the partial `fund.json`. Skipped → `"agentId": 0` (always means “not minted”, never a placeholder id).

**Friction note (2026-09-20 desk run):** the beta fund `grok-fund-beta` was created with `agentId 0`; the agent wallet holds no identity on mainnet and no mainnet ETH. The Privy sign → raw path to `rpc.mainnet.chain.robinhood.com` is the same shape as the fork dust self-send but was not smoke-tested on mainnet during that run.

## 3 — Create fund (then fund.json)

1. Confirm params with owner (**explicit yes**): name, subdomain, description, asset (**USDG** on fork `9994663`), `--open-deposits` / `--public-chat`, agent id (`agentId` from `fund.json` — step 2 token id, or `0` if skipped).
2. Prepare 10k WOOD owner stake → `sherwood --calldata-only vault create --agent-id <agentId> …` → Privy broadcast.
3. Register agent wallet on vault (`vault add`).
4. **Then** write `workspace/fund.json` (`vault`, `agent`, `agentId`, `subdomain`, `rpc`, `chainId`). Do not invent a vault address. See `workspace/fund.json.example`.
5. Optional: deposit dust USDG so totalAssets &gt; 0.

Chain id for incentivized beta: `9994663` (confirm against current Sherwood skill if it drifts).

## 4 — Roster (personas / bots)

Create the six teammates from `agents/*.md` **before** relying on a recurring paper loop:

Scanner → Research → Critic → PM → Risk → Ops.

Role-routing inside Desk Lead is OK for a one-shot dry run only — say so explicitly. Share-as-Template clones Lead only; roster is not automatic.

Personas are **lenses** in `personas/` (tags), not LARPing as real people.

## 5 — Paper loop

Only after wallet + identity (or recorded skip) + fund (+ roster or waiver):

1. Scanner → `briefs/scan.md`
2. Research → `briefs/research.md` (**Critic waits on this** — not true parallel)
3. Critic → `briefs/critic.md`
4. PM → `proposals/draft.json`
5. Risk → `proposals/risk.md` (APPROVE | REJECT | REVISE)

No Ops / chain on paper. Risk REJECT → bounce to PM (or Research on quality kill). Never bully Ops.

**RH basis:** If Scanner cannot get RH-fork token mids vs US cash, mark `rhBasis: UNKNOWN` early and keep `liveReady: false`. Do not burn two full loops discovering this at Risk.

## 6 — Live Ops

Ops only after Risk **APPROVE**, known RH basis (or accepted haircut), and Privy sign → raw broadcast discipline. One live PortfolioStrategy at a time. If `fund.json` still has `agentId: 0`, remind the owner once that production requires the step-2 mint.

---

## Session feedback (Grok Fund Beta, 2026-09-20)

What worked: explicit create confirm before gas; Privy `list-wallets` → agent address; artifacts in `workspace/` made REJECT auditable.

What hurt: scrambled order (paper before vault); Privy stake path not one-click; roster never bootstrapped; cadence text implied Research∥Critic; RH basis hole; too many “what’s next?” widgets; identity mint never offered (fund created with `agentId 0`).

**Product rule:** linear installer — (1) Privy + faucet, (2) ERC-8004 identity on RH mainnet (optional on fork, required on prod), (3) create fund + deposit dust, (4) spawn six bots, (5) morning paper loop — refuse skip-ahead.
