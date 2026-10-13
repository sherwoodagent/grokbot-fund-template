---
name: research-byok
description: Use for research signal before proposing. Messari is the default (x402, MetaMask signs), Nansen the fallback; Bankless and X are optional with the owner's own key.
---
# Research

**Default: Messari**, paid per call via x402 with the agent wallet signing (MetaMask). Keep a little USDC on Base in the agent wallet. **Fallback: Nansen**, if Messari is down or the deadline can't be met. Bankless and X are optional and use the owner's **own** key or account.

> CLI note: `sherwood research` needs a MetaMask signer mode so sign-only agents can pay through it (pending). Until it ships, use the two-step flow below if your CLI supports it, otherwise tell the owner research is unavailable and propose from `/sources` alone.

- **Bankless:** the owner's own Bankless Premium key or MCP. Use it for protocol launches, risks and metrics. Link the posts and don't repost Premium text.
- **Messari / Nansen:** via `sherwood research … --provider <messari|nansen>`, paid per call in USDC on Base (x402). See `sherwood research --help` for subcommands.
- **X:** whichever X connector the owner has connected.

## Paying for Messari / Nansen (each call costs money)
Needs USDC on Base (8453) in the agent wallet, and a wallet that can sign EIP-712 typed data (MetaMask: `mm wallet sign-typed-data --chain-id 8453 --payload '<typed_data JSON>' --wait`; Privy: `eth_signTypedData_v4` via its rpc). No private key. The CLI builds the x402 payment header itself; you only sign.
1. **Ask the owner first, every call:** provider, query and the cost. One yes covers one call. Never pass `--yes` from a routine or any unattended run.
2. Step 1: `sherwood research <token|market|smart-money|wallet> … --provider <messari|nansen> --address <wallet> --json`. It confirms the cost, then prints an `x402_signature_request` (typed data plus a deadline) and **waits**. Keep it running.
3. Sign the `typed_data` object from `x402_signature_request` with the wallet before the deadline (MetaMask: pass it as `--payload`, `--chain-id 8453`).
4. Step 2: `sherwood research complete --signature 0x… --address <wallet>`. The waiting step 1 pays, retries and prints the result.
5. A Messari `market` query needs two payments: step 2 prints a second `x402_signature_request` and exits 0. Sign it and run `research complete` again.

**Deadlines:** Messari's window is short (about a minute), Nansen's is longer; the CLI prints the exact deadline. MetaMask signing without a per-signature human approval fits Messari's window. If a person must approve each signature, use Nansen. If the deadline passes, nothing was paid. Ask the owner again before retrying.

Output: a short `## Signals` section in the proposal's `draft.md`, with a link and date for each claim. Signals inform the thesis. They never override the policy in BOT.md.
