---
name: research-byok
description: Use for research signal before proposing. Messari is the default (x402, MetaMask signs), Nansen the fallback; Bankless and X are optional with the owner's own key.
---
# Research

**Default: Messari**, paid per call via x402 with the agent wallet signing (MetaMask). Keep a little USDC on Base in the agent wallet. **Fallback: Nansen**, if Messari is down or the deadline can't be met. Bankless and X are optional and use the owner's **own** key or account.

- **Messari (default):** call the Messari API directly and pay per call with x402 (below). Endpoints: Messari's API docs.
- **Nansen (fallback):** via `sherwood research … --provider nansen` (x402, USDC on Base; the CLI prints a typed-data request, you sign, then `sherwood research complete --signature 0x… --address <wallet>`). Use it if Messari is down or its window can't be met.
- **Bankless:** the owner's own Bankless Premium key or MCP. Link the posts and don't repost Premium text.
- **X:** whichever X connector the owner has connected.

## Paying Messari directly (x402, each call costs money)
Keep a little USDC on Base (8453) in the agent wallet. No private key: the wallet only signs.
0. **Ask the owner first, every call:** query and the cost. One yes covers one call. Routines never pay.
1. **Call** the Messari endpoint with no payment. Expect `402` with an x402 v2 body. From `accepts`, pick the entry with `scheme: "exact"` on Base for USDC. Read `amount`, `payTo`, `asset`, `maxTimeoutSeconds` and the EIP-712 domain (`extra.name`, `extra.version`) from that response. Never hardcode a price or address.
2. **Build** an EIP-3009 `TransferWithAuthorization` typed-data payload: domain `{name, version, chainId: 8453, verifyingContract: asset}`; message `{from: <agent wallet>, to: payTo, value: amount, validAfter: now − 600, validBefore: now + maxTimeoutSeconds, nonce: <random 32 bytes, 0x-hex>}`.
3. **Sign** it: `mm wallet sign-typed-data --chain-id 8453 --payload '<typed data JSON>' --wait`. If it prints an MFA/approval link, send that link to the owner right away and wait for their approval.
4. **Retry immediately** with the same request plus the payment header: base64 of the x402 v2 payload (`x402Version`, the chosen `accepts` entry, `payload: {signature, authorization}`) in `PAYMENT-SIGNATURE` (v2) or `X-PAYMENT` (v1 servers). Steps 2–4 must finish inside `maxTimeoutSeconds`. If the window passes, nothing was paid; ask the owner before retrying.

Output: a short `## Signals` section in the proposal's `draft.md`, with a link and date for each claim. Signals inform the thesis. They never override the policy in BOT.md.
