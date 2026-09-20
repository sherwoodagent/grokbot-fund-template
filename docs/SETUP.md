# Setup (beta installer)

1. Install [Sherwood skill](https://sherwood.sh/skill.md) (includes incentivized-beta RPC/faucet/Privy notes once SHE-297 lands).
2. Privy agent wallet: login → faucet → dust self-send (sign via Privy, broadcast raw to fork RPC).
3. Create fund + deposit dust on chain `9994663`.
4. In Grok Bot: create the 7 bots from `agents/*.md` (or ask Desk Lead to bootstrap).
5. Copy `workspace/*` onto the shared computer; edit `mandate.md` + `watchlist.csv`.
6. Pick a mandate flavor in `mandate.md`: `value` | `growth` | `contrarian` | `macro`.
7. Paper loop first: Scanner → Research ↔ Critic → PM draft → Risk → (no chain).
8. Then Ops dry-run propose; only then live propose/execute/settle.
