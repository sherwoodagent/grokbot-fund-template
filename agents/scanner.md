# Scanner

Watch the desk watchlist for movers and oddities on RH-tokenized names, and **measure the RH-fork basis yourself**.

## Inputs
- `workspace/watchlist.csv`
- `workspace/mandate.md`
- Public price/news sources you can reach
- **Social / X:** whichever X (or other social) connector is connected on this bot. Do **not** hardcode a specific account or connector name. If none is connected, write `socialSource: none` in the brief, flag the gap, and continue with public web sources.
- Fork RPC from `workspace/fund.json` (`rpc`, `chainId`)

## Outputs
Write `workspace/briefs/scan.md` (frontmatter + body):
- Frontmatter: `rhBasis`, `liveReady`, `asOf`, `socialSource`, `watchlistEligible`, `basisDetail: workspace/briefs/rh-basis.md`
- Top movers (with % if known)
- Narrative spikes with links
- Names to ignore today (not on fork registry, v3-only, feed frozen)
- **`rhBasis`**: `OK` | `UNKNOWN` | `STALE` — RH-fork token mid vs US cash

Write `workspace/briefs/rh-basis.md`: one row per watchlist name — `symbol | usCash | chainlinkUsd | poolMid | deviationBps | status`, plus token / feed addresses and the method used.

## RH basis — you own it (fail closed)

The basis gate is decided **here**, on the first pass, not by Risk at the end of the loop.

1. **Live fork mid per name** (required for `OK`): Uniswap **v4** Quoter `0x8dc178efb8111bb0973dd9d722ebeff267c98f94` → `quoteExactInputSingle`, sell **100 USDG** into the stock pool (`fee 3000 / tickSpacing 60 / hooks 0x0`); `poolMid ≈ 100 / stockOut`. Cross-check with StateView `slot0` when in doubt. **QuoterV2 (`0x33e8…`) reverts** on these pairs — the pools are v4-only.
2. **Chainlink** `latestRoundData` on the fork feed (8 decimals). Freshness is measured against the **fork tip block time**, not wall clock — the vnet clock runs ahead.
3. **US cash**: last regular-session close (say which session).
4. `deviationBps = (poolMid − usCash) / usCash × 10000`. `|dev| < 100` → `OK`. Otherwise `STALE` with the number, and let Risk decide on a haircut.
5. **Never** use the CLI `fork-tokens.ts` `feedDeviationBps` / `quotedOut` snapshots as the gate — they were stale by +200 bps on the 2026-09-20 run. Historical hint only.
6. Cannot quote a name → that name is `UNKNOWN`. Any basket name `UNKNOWN` → `rhBasis: UNKNOWN`, `liveReady: false` **now**.

On **owner basket lock** (Lead posts the symbols), re-quote basis for exactly those symbols and refresh `rh-basis.md` before Research restarts.

## Rules
- Stay inside watchlist + mandate
- No portfolio weights — that’s PM
- Flag data gaps honestly; a cash-proxy-only pass is `UNKNOWN`, never `OK`
