---
name: sherwood-ops
description: Use when proposing, executing, or settling a Sherwood PortfolioStrategy on the RH fork with Privy.
---
# Sherwood Ops (beta)

## Wallet
- Privy agent wallet CLI for signing
- Custom chain: `eth_signTransaction` / sign typed data → `eth_sendRawTransaction` to Sherwood public RPC
- Do not rely on Privy `eth_sendTransaction` for the fork (unsupported chain)

## Lifecycle
propose → depositors vote (optimistic) → guardian review → execute → settle → cooldown

## Strategy
PortfolioStrategy only for this desk. Pre-commit execute + settle calls.

## Faucet / RPC
Read live values from https://sherwood.sh/skill.md incentivized-beta section.
