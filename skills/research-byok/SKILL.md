---
name: research-byok
description: Optional. Use when the owner wants extra signal from their own research sources (Bankless with their own key, X, Messari, Nansen) before proposing.
---
# Research (bring your own key)

Optional and off by default. Only use sources the owner has connected with **their own** key or account.

- **Bankless:** the owner's own Bankless Premium key or MCP. Use it for protocol launches, risks and metrics. Link the posts and don't repost Premium text.
- **Messari / Nansen:** via `sherwood research … --provider <messari|nansen>`, paid per call in USDC on Base (x402). See `sherwood research --help` for subcommands.
- **X:** whichever X connector the owner has connected.

## Paying for Messari / Nansen (each call costs money)
Needs USDC on Base (8453) in the agent wallet, and a wallet that can sign EIP-712 typed data (MetaMask: `{{MM_SIGN_TYPED_DATA}}`; Privy: `eth_signTypedData_v4` via its rpc). No private key.
1. **Ask the owner first, every call:** provider, query and the cost. One yes covers one call. Never pass `--yes` from a routine or any unattended run.
2. Step 1: `sherwood research <query> --provider <messari|nansen> --address <wallet> --json`. It confirms the cost, then prints an `x402_signature_request` (typed data plus a deadline) and **waits**. Keep it running.
3. Sign the `typed_data` with the wallet before the deadline.
4. Step 2: `sherwood research complete --signature 0x…`. The waiting step 1 pays, retries and prints the result.
If the deadline passes, nothing was paid. Ask the owner again before retrying.

Output: a short `## Signals` section in the proposal's `draft.md`, with a link and date for each claim. Signals inform the thesis. They never override the policy in BOT.md.
