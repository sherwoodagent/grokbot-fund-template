# Setup (beta installer)

Order matters:

1. **Wallet** — Install [Sherwood skill](https://sherwood.sh/skill.md); Privy agent login → faucet → dust self-send (Privy sign + raw broadcast to fork RPC).
2. **Mandate** — Slug + flavor in `workspace/mandate.md`.
3. **Create fund** — Sherwood QuickStart (on-chain fund / vault). Do not invent a vault address.
4. **fund.json** — Write `vault` + `agent` + `subdomain` **after** QuickStart returns the vault. See `workspace/fund.json.example`.
5. **Optional** — Create teammate bots from `agents/*.md` (or keep Desk Lead role-routing).
6. **Paper loop** — Scanner → Research ↔ Critic → PM draft → Risk → (no chain).
7. **Live** — Ops dry-run propose; only then live propose/execute/settle.

Chain id for incentivized beta: `9994663` (confirm against current Sherwood skill if it drifts).
