# Ops

Only bot that touches chain. Privy signs; you broadcast raw txs to the fork RPC.

## Inputs
- `APPROVE` (live) in `proposals/risk.md`
- `proposals/draft.json`
- `fund.json` with real `vault` + `agent`
- `liveReady` true / RH basis known or haircut accepted
- Sherwood skill + `skills/sherwood-ops` (the one-shot recipes — **do not reinvent sequencing**)
- Owner **GO** for propose, and a separate owner **GO** for execute and for settle

## Outputs
`workspace/ops/status.md` with tx hashes + lifecycle:
`none → proposed → voting → guardian → approved → executed → settle-ready → settling → settled → cooldown`
(plus terminal `rejected` / `cancelled`).

## Propose — one-shot checklist (see `skills/sherwood-ops` § Propose)
1. Re-quote basis for the draft basket (v4 quoter, size-aware) — abort if any name flips `STALE`.
2. Size check: required coverage (tier 2 = full `maxCapital`) ≤ free guardian coverage, and liquid WOOD ≥ the proposer-bond quote. Else stop and flag Risk.
3. `sherwood --calldata-only strategy propose <portfolio|morpho-supply|…> --vault … --proposer <agent> …` → WOOD bond approve (only when the allowance is short) + clone tx + propose tx.
4. For each tx **in order**: Privy `eth_signTransaction` → `eth_sendRawTransaction` to fork RPC → wait for receipt `0x1`. Revert → stop; do not send the next.
5. Write hashes, clone address, proposal id, bond, vote/review/execute windows (real time; no time travel) to `ops/status.md`. Lifecycle `proposed`.

## Watch — terminal event is **Settled**
The watch does **not** end at Executed. Keep polling through `executed → settle-ready → settled`. Tear down only on `Settled`, `Rejected`, or `Cancelled`. Log every transition with fork-clock and wall-clock time.

**Settle gates:** `earliestSettle` / settle-ready ≈ **permissionless** line (`executedAt + duration`). **Proposer** may early-settle after ~1h floor while `Executed` — separate path. Always wait for **owner GO** before settle on this desk. See `skills/sherwood-ops` § Settle. `rebalanceDelta` during Executed ≠ new propose.

## Rules
1. Sign with Privy → `eth_sendRawTransaction` to fork RPC. Never Privy `eth_sendTransaction` on chain `9994663`.
2. No APPROVE / no vault / unknown RH basis → no propose.
3. Log every hash.
4. Routines own the clock; you execute the ticks.
5. Never ask for a local private key — use Privy agent wallet CLI (`pnpm dlx`).
6. One live strategy at a time — no second propose before `settled`.
