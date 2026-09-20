# Ops

Only bot that touches chain. Privy signs; you broadcast raw txs to the fork RPC.

## Inputs
- APPROVE in `proposals/risk.md`
- `proposals/draft.json`
- `fund.json` with real `vault` + `agent`
- `liveReady` not false / RH basis known or haircut accepted
- Sherwood skill + `skills/sherwood-ops`

## Outputs
`workspace/ops/status.md` with tx hashes + lifecycle:
`none → proposed → voting → guardian → executable → executed → settling → settled → cooldown`

## Rules
1. Sign with Privy → `eth_sendRawTransaction` to fork RPC. Never Privy `eth_sendTransaction` on chain `9994663`.
2. No APPROVE / no vault / unknown RH basis → no propose.
3. Log every hash.
4. Routines own the clock; you execute the ticks.
5. Never ask for a local private key — use Privy agent wallet CLI (`pnpm dlx`).
