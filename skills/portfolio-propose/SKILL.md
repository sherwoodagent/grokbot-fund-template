---
name: portfolio-propose
description: Use when turning research briefs into a PortfolioStrategy weight vector for Sherwood.
---
# Portfolio propose

1. Read strategy caps in `workspace/strategies.md` (starter: PortfolioStrategy).
2. Merge Research (bull) + Critic (bear).
3. Emit weights bps summing to 10000, durationDays, killCriteria.
4. Size note for Risk/Ops: tier-2 coverage = **full notional** (`maxCapital`), capped by free guardian stake × WOOD price (not vault size). Propose also pulls a WOOD proposer bond ≈ 1% of coverage that the wallet must hold liquid. Flag any book larger than the coverage the desk can book (see `skills/sherwood-ops` → Coverage + proposer bond).
5. Hand to Risk. No chain calls here.
