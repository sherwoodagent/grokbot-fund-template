# Setup (beta installer)

**Order is mandatory.** Desk Lead must refuse to skip ahead. Prefer a sticky checklist over re-prompting “what’s next?”

| Step | Gate |
|------|------|
| 1. Wallet | Privy logged in + faucet/ETH check |
| 2. Fund | On-chain vault exists; `fund.json` written |
| 3. Roster | Six bots from `agents/*.md` (or explicit role-route waiver) |
| 4. Paper | Morning loop only after 1–3 |
| 5. Live | Risk APPROVE + known RH basis |

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

## 2 — Create fund (then fund.json)

1. Confirm params with owner (**explicit yes**): name, subdomain, description, asset (**USDG** on fork `9994663`), `--open-deposits` / `--public-chat`, agent id.
2. Prepare 10k WOOD owner stake → `sherwood --calldata-only vault create …` → Privy broadcast.
3. Register agent wallet on vault (`vault add`).
4. **Then** write `workspace/fund.json` (`vault`, `agent`, `subdomain`, `rpc`, `chainId`). Do not invent a vault address. See `workspace/fund.json.example`.
5. Optional: deposit dust USDG so totalAssets &gt; 0.

Chain id for incentivized beta: `9994663` (confirm against current Sherwood skill if it drifts).

## 3 — Roster (personas / bots)

Create the six teammates from `agents/*.md` **before** relying on a recurring paper loop:

Scanner → Research → Critic → PM → Risk → Ops.

Role-routing inside Desk Lead is OK for a one-shot dry run only — say so explicitly. Share-as-Template clones Lead only; roster is not automatic.

Personas are **lenses** in `personas/` (tags), not LARPing as real people.

## 4 — Paper loop

Only after wallet + fund (+ roster or waiver):

1. Scanner → `briefs/scan.md`
2. Research → `briefs/research.md` (**Critic waits on this** — not true parallel)
3. Critic → `briefs/critic.md`
4. PM → `proposals/draft.json`
5. Risk → `proposals/risk.md` (APPROVE | REJECT | REVISE)

No Ops / chain on paper. Risk REJECT → bounce to PM (or Research on quality kill). Never bully Ops.

**RH basis:** If Scanner cannot get RH-fork token mids vs US cash, mark `rhBasis: UNKNOWN` early and keep `liveReady: false`. Do not burn two full loops discovering this at Risk.

## 5 — Live Ops

Ops only after Risk **APPROVE**, known RH basis (or accepted haircut), and Privy sign → raw broadcast discipline. One live PortfolioStrategy at a time.

---

## Session feedback (Grok Fund Beta, 2026-09-20)

What worked: explicit create confirm before gas; Privy `list-wallets` → agent address; artifacts in `workspace/` made REJECT auditable.

What hurt: scrambled order (paper before vault); Privy stake path not one-click; roster never bootstrapped; cadence text implied Research∥Critic; RH basis hole; too many “what’s next?” widgets.

**Product rule:** linear installer — (1) Privy + faucet, (2) create fund + deposit dust, (3) spawn six bots, (4) morning paper loop — refuse skip-ahead.
