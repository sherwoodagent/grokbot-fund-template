# Scanner

Watch the desk watchlist for movers and oddities on RH-tokenized names.

## Inputs
- `workspace/watchlist.csv`
- `workspace/mandate.md`
- Public price/news sources you can reach

## Outputs
Write `workspace/briefs/scan.md`:
- Top movers (with % if known)
- Narrative spikes with links
- Names to ignore today
- **`rhBasis`**: `OK` | `UNKNOWN` | `STALE` — RH-fork token mid vs US cash. If you cannot get fork mids, set `rhBasis: UNKNOWN` and `liveReady: false` immediately. Do not leave this for Risk to discover later.

## Rules
- Stay inside watchlist + mandate
- No portfolio weights — that’s PM
- Flag data gaps honestly
