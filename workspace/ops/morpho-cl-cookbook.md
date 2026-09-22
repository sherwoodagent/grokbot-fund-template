# Morpho + ConcentratedLiquidity cookbook — RH fork 9994663

**As of:** 2026-09-22 ~12:33 America/Bogota (COT)  
**Scope:** Discovery only. Proposal #2 untouched. No broadcast / no propose.  
**RPC:** `Tenderly RH-fork RPC (see SETUP — do not commit private fork URLs)`  
**Vault asset USDG:** `0x5fc5360D0400a0Fd4f2af552ADD042D716F1d168` (6 decimals)  
**WETH:** `0x0Bd7D308f8E1639FAb988df18A8011f41EAcAD73`

---

## Executive summary

| Sleeve | On-fork venue? | Concrete IDs? | CLI propose? | Ops posture |
|--------|----------------|---------------|--------------|-------------|
| **S1 MorphoSupply** | **YES** — Morpho Blue + many USDG loan markets | **YES** — marketIds below | **NO** — no `morpho-supply` CLI key | **Conditional CLEARED for Ops** (IDs verified; manual clone path only; dry-run init still required) |
| **S3 ConcentratedLiquidity** | Partial — V3 WETH/USDG pools live; CL template is **V3+Morpho leverage**, not v4 equity LP | Pool addrs + one candidate Morpho market id | **NO** — no `concentrated-liquidity` CLI key | **Still BLOCKED** — allowlist + borrow liquidity + no CLI |

Equity Uniswap **v4** stock/USDG pools (AAPL/MSFT/AMZN/SPY …) do **not** unblock S3: the ConcentratedLiquidity template binds **Uniswap V3** `IUniswapV3Pool` + `NonfungiblePositionManager`, funds the LP via **Morpho borrow**, and Critic’s caveat stands.

---

## How discovery was done

1. Confirmed Tenderly fork = RH mainnet state (same addresses as chain 4663).
2. Sherwood docs / skill.md: MorphoSupply + CL templates deployed on fork; CLI only builds `portfolio`.
3. `eth_getCode` on templates + Morpho Blue + Uniswap infra on fork RPC.
4. Morpho API (`api.morpho.org` GraphQL, `chainId: 4663`) for market list → `eth_call` `idToMarketParams` / `market` on fork to verify.
5. Uniswap: v3 `getPool(WETH,USDG,fee)` + v4 StateView `getSlot0`/`getLiquidity` for equity/USDG keys.
6. Strategy sources: `sherwoodagent/sherwood-protocol` `MorphoSupplyStrategy.sol` / `ConcentratedLiquidityStrategy.sol`.
7. `StrategyFactory.approvedTemplate` + `TierRegistry.isCounterpartyAllowed` on fork.

---

## Shared addresses (verified `HAS_CODE` on fork)

| Role | Address | Note |
|------|---------|------|
| Morpho Blue | `0x9D53d5E3bd5E8d4Cbfa6DB1ca238AEA02E651010` | `owner()` + `DOMAIN_SEPARATOR()` match RH toolkit record |
| AdaptiveCurve IRM | `0x2BD3d5965B26B51814AC95127B2b80dD6CcC0fa1` | `isIrmEnabled=true` |
| MorphoSupplyStrategy template | `0x5B55E1Da361573CB0788e750038567D2569BE41d` | `name()=Morpho Supply`; **approvedTemplate=true** |
| ConcentratedLiquidityStrategy template | `0xcba9C84F2d382729D1519c5F7f4a9AAaC075f8B5` | `name()=Concentrated Liquidity LP`; **approvedTemplate=true** |
| PortfolioStrategy template | `0xAA5872009c527cCb80343E41C52840CEdb095eb0` | CLI path only |
| StrategyFactory | `0xb06788F027268a9A06c3AD41a88559530D2E54b1` | `cloneAndInit` / `cloneAndInitDeterministic` |
| TierRegistry | `0x4614f058920941A0a9a852e62485fbDE692D75E1` | Via vault governor |
| UniswapSwapAdapter | `0x54E6A7af53143556973493fDeC9d7837A77c67eF` | |
| Uniswap V3 Factory | `0x1f7d7550B1b028f7571E69A784071F0205FD2EfA` | |
| V3 NonfungiblePositionManager | `0x73991a25c818bf1f1128deaab1492d45638de0d3` | |
| V4 PoolManager | `0x8366a39CC670B4001A1121B8F6A443A643e40951` | Portfolio / basis only — **not** CL template |
| V4 StateView | `0xf3334192d15450cdd385c8b70e03f9a6bd9e673b` | |
| V4 Quoter | `0x8dc178efb8111bb0973dd9d722ebeff267c98f94` | Desk basis probes |
| steakUSDG (Vault V2) | `0xBeEff033F34C046626B8D0A041844C5d1A5409dd` | ERC-4626 `asset()=USDG`; deposits gated |

Morpho Blue `DOMAIN_SEPARATOR` (fork eth_call): `0xdec2c0a13cb9b2c7a749851d2692c8fd3a7941bf77148fced920e78c99a5fba0`.

---

## S1 — MorphoSupply

### Verdict: **ON FORK** (markets + template). CLI builder **missing**.

### Init calldata (from strategy source)

```text
abi.decode(data) = (address morpho, MarketParams marketParams, uint256 supplyAmount)

MarketParams = {
  address loanToken;       // MUST == vault.asset() == USDG
  address collateralToken; // borrower collateral; not bound by allowlist in MorphoSupply
  address oracle;
  address irm;
  uint256 lltv;            // WAD
}
marketId = keccak256(abi.encode(MarketParams))  // verified for AAPL market below
```

Lifecycle (source natspec):

- **Execute:** `[USDG.approve(clone, supplyAmount), clone.execute()]` → supply to Morpho onBehalf=clone  
- **Settle:** `[clone.settle()]` → withdraw **by shares** (interest included); if utilization blocks full exit → `SettlementIncomplete` + residue via `sweep()`  
- **updateParams:** always reverts (`NoTunableParams`)

### Flagship verified market (recommended Ops default)

| Field | Value (fork eth_call) |
|-------|------------------------|
| **marketId** | `0xdeb4782d012d5fd3b24962538c2f6559049d70bda4dabd2e4212dacb96c28d45` |
| loanToken | USDG `0x5fc5360D0400a0Fd4f2af552ADD042D716F1d168` |
| collateralToken | AAPL `0xaF3D76f1834A1d425780943C99Ea8A608f8a93f9` |
| oracle | `0xD625d488D552775D2867194C618B945E5dDfE097` |
| irm | `0x2BD3d5965B26B51814AC95127B2b80dD6CcC0fa1` |
| lltv | `625000000000000000` (62.5%) |
| totalSupplyAssets | `239059356821` (~239,059 USDG) |
| totalBorrowAssets | `101129325` (~101 USDG) |
| API util / supply APY | ~0.04% util · ~0.00024% APY (near-idle — float sleeve, not yield chase) |

`keccak256(abi.encode(MarketParams))` **matches** marketId on fork.

### Other USDG loan markets (fork-verified sample, by API supplyUsd)

| marketId | collateral | lltv | fork totalSupplyAssets | notes |
|----------|------------|------|------------------------|-------|
| `0xf47c7a7a1ff6c7444e6fcfa20a71f439e4525f4f9640fe6ba5c080f6f2a9d33f` | wsNET | 38.5% | 1602105783 | ~$1.6k |
| `0xedc213c29e117c66ae8deb3b53af2ce3b66a9011c2152e83e412b2c5cd40ea3e` | BIGTECH | 38.5% | 181984030 | util 100% — avoid |
| `0xf6f3dbe0a19e948147e79e66502c6b05709d8dfde977fec7db1528fc8f0ebdfa` | SGOV | 86% | 110980357 | idle |
| `0xf4dff250826a86627545e5c6594b3b249db3ad2ec5eed56c02833d2a67acf445` | TSLA | 77% | 15012678 | higher util |
| `0xc845da65a020ddca5f132efa8fea79676d8edfdea504226a4c01e7a9e34cddd6` | USDe | 91.5% | (API ~$337M) | large; re-verify before use |

Morpho API returned 100 USDG-loan markets on chain 4663; fork state mirrors params. Full table: Morpho API `markets(where:{chainId_in:[4663], loanAssetAddress_in:[USDG]})`.

WETH-loan markets exist (3) but vault asset is USDG → MorphoSupply would revert `LoanAssetMismatch`.

### CLI

```bash
sherwood strategy list          # only Portfolio on robinhood-fork
sherwood strategy propose --help  # no morpho / concentrated-liquidity keys
```

skill.md: *“MorphoSupply and ConcentratedLiquidity templates are deployed but have no CLI key … cannot be cloned through the CLI.”*

### Manual propose sketch (NOT executed — HOLD)

Requires agent wallet as `msg.sender` for `StrategyFactory.cloneAndInit` / deterministic variant, then `governor.propose` with execute/settle batches. Sketch only:

```text
1) Encode initData = abi.encode(
     morpho = 0x9D53d5E3bd5E8d4Cbfa6DB1ca238AEA02E651010,
     MarketParams{USDG, AAPL, oracle, IRM, 625000000000000000},
     supplyAmount  // raw USDG units, 6 decimals
   )
2) StrategyFactory.cloneAndInitDeterministic(
     template = 0x5B55E1Da…BE41d,
     vault, proposer, initData, salt
   )   # template approvedTemplate == true on fork
3) Build calls:
     execute: [USDG.approve(clone, amount), clone.execute()]
     settle:  [clone.settle()]
4) proposal create — DO NOT broadcast until OWNER unlocks post-#2
```

There is **no** `sherwood strategy propose morpho-supply …` today.

### Allowlist / init risk (honest)

- Master-branch MorphoSupply requires `TierRegistry.isAdapterAllowed(morpho)` at init.
- Fork `TierRegistry` bytecode **contains** `isCounterpartyAllowed` but **does not** contain `isAdapterAllowed` / `setAdapterAllowed` selectors.
- Morpho **is** `isCounterpartyAllowed=true` on fork.
- Deployed MorphoSupply bytecode embeds `MorphoNotAllowed` but **not** the `isAdapterAllowed` selector → exact init gate on this build is uncertain.
- **Ops must dry-run** `cloneAndInit` + `initialize` simulation (calldata-only / Tenderly sim) before any live propose. Do not assume allowlist is clear.

### S1 blockers remaining

1. No CLI builder / propose subcommand.  
2. Dry-run init vs TierRegistry Morpho standing not proven.  
3. Near-idle AAPL market → floating sleeve, not meaningful APY.  
4. Proposal #2 still Pending HOLD — no sequential propose until Settled.

---

## S3 — ConcentratedLiquidity

### Verdict: **BLOCKED** for Ops live. Not “NOT ON FORK” for infra — **path incomplete**.

### What the template actually is

From `ConcentratedLiquidityStrategy` natspec (not a vanilla LP):

1. Pull USDG → post as Morpho **collateral** (or deposit into ERC-4626 wrapper of USDG).  
2. **Borrow** USDG from that Morpho market.  
3. Swap fraction into pool **otherToken**.  
4. Mint **one Uniswap V3** NFT position via `NonfungiblePositionManager`.  
5. Settle: burn/collect → swap other→USDG → repay Morpho → withdraw collateral → push to vault.

`InitParams` (abridged): `pool`, `positionManager`, `uniswapFactory`, `swapAdapter`, `morpho`, `MarketParams`, `collateralAmount`, `borrowAmount`, `tickLower`/`tickUpper`, TWAP/slippage/rerange policy, `swapExtraData`.

Constraints:

- `marketParams.loanToken == USDG`  
- `marketParams.collateralToken == USDG` **or** ERC-4626 with `asset()==USDG` (not the volatile leg)  
- Pool is **V3**, proven via factory `getPool`  
- Swap adapter needs **adapter** standing; Morpho / PosM / factory / otherToken / wrapper need **counterparty** standing  

### Uniswap V3 WETH/USDG pools (ON FORK)

| fee | pool | liquidity (fork) |
|-----|------|------------------|
| 100 | `0x52e65B17fB6E5BA00Ed806f37Afcd2DaA50271Ca` | 3962599041251469572 |
| 500 | `0x69BfaF19C9f377BB306a89aEd9F6B07e2c1a8d9a` | 860664105469974835 |
| 3000 | `0xa9188730Fe85Be88ad499D7d52B099e800fB0334` | 256700317172643899 |
| 10000 | `0x5f009E071F07e92B6C624e83F52F17bBDa34680D` | 75922745803895277 |

token0=WETH, token1=USDG. V3 Factory + PosM **isCounterpartyAllowed=true**.

### Equity / USDG Uniswap **v4** pools (ON FORK — Portfolio/basis only)

fee `3000` / tickSpacing `60` / hooks `0x0` — StateView liquidity > 0 for AAPL, MSFT, AMZN, SPY, WETH. Example poolId AAPL: `0xc748f4671a867db48b552f6b7650bf3255e05f80f00e3f7aad1b17ccb7898fdb`.  

**Not usable as CL LP legs** with current template (V3-only). Matches Critic.

### Candidate Morpho market for CL collateral path (ON FORK)

| Field | Value |
|-------|--------|
| marketId | `0xae1c6160c3f3dd7884579d235b48f66d18770e6d27be29c59abe344d44ac67ce` |
| loan | USDG |
| collateral | vUSDG `0xcf3a65a3b54241577881FAa7003E4335247F3FB2` (`asset()=USDG` ✓) |
| oracle | `0xb49940deca4190520a5c31Ab1AD6f57e69cF47EF` |
| irm | AdaptiveCurve |
| lltv | 91.5% |
| fork lendable | **~6.00 USDG** (`supply−borrow`) — **unusable for meaningful size** |

No Morpho market found with `loan=USDG` and `collateral=USDG` or `steakUSDG` in the API window. Markets with USDG **as collateral** lend stocks (wrong direction for this template).

### TierRegistry gaps that keep S3 blocked

| Address | Needed for | `isCounterpartyAllowed` (fork) |
|---------|------------|--------------------------------|
| Morpho | CL bind | **true** |
| V3 PosM | CL bind | **true** |
| V3 Factory | CL bind | **true** |
| SwapAdapter | CL (adapter axis) | counterparty true; **`isAdapterAllowed` absent on this TierRegistry build** |
| **WETH** | pool otherToken | **false** |
| **vUSDG** | Morpho collateral wrapper | **false** |

Even after listing WETH + vUSDG, SwapAdapter strong-axis standing and Morpho borrow depth remain blockers. No CLI key.

### S3 blockers list

1. No CLI `concentrated-liquidity` builder.  
2. Template ≠ equity v4 LP; v4 stock/USDG pools do not qualify.  
3. WETH not counterparty-allowed (blocks WETH/USDG V3 otherToken).  
4. vUSDG not counterparty-allowed (blocks only verified USDG-wrapper Morpho market).  
5. Candidate Morpho market lendable ~6 USDG.  
6. Fork TierRegistry missing `isAdapterAllowed` (CL requires it for swapAdapter).  
7. Complex InitParams (ticks, TWAP, rerange, expectedLiquidity) need off-chain agent math + guardian review — not Ops-ready without builder.

### CLI propose sketch

**None.** Manual `cloneAndInit` + hand-built `InitParams` only after allowlist + liquidity gates clear — out of scope until then.

---

## Cross-cutting blockers

- `sherwood strategy list` → Portfolio only; Morpho/CL under peer “Not available” / omitted keys.  
- Proposal **#2** Pending / execute **HOLD** — no satellite propose.  
- Do not invent pool keys or market IDs beyond eth_call-verified rows above.

---

## Pointers

- Desk: `briefs/satellite-options.md` (S1/S3 status)  
- Basis / v4 keys: `briefs/rh-basis.md`  
- RH Morpho record: [nirholas/robinhood-toolkit DEPLOYMENTS](https://github.com/nirholas/robinhood-toolkit/blob/main/examples/08-morpho-lending/lend/DEPLOYMENTS.md)  
- RH Uniswap record: [dex/DEPLOYMENTS.md](https://github.com/nirholas/robinhood-toolkit/blob/main/dex/DEPLOYMENTS.md)  
- Sherwood skill: https://sherwood.sh/skill.md

---

## Init dry-run status (2026-09-22 add-on)

**MorphoSupply `cloneAndInit` / `initialize` dry-run: PASS** on fork `9994663` (eth_call / sim / trace only; TierRegistry Morpho allowed; dust 1 USDG). Full write-up: `workspace/ops/morpho-init-dryrun.md`.

Implications for template desks:
- **Init gate cleared** for the **manual StrategyFactory** path.
- Live propose still blocked until: CLI `morpho-supply` ships **or** a documented manual recipe is used, and only **after proposal #2** is clear (post-#2 only).
- Do not treat dry-run PASS as CLEARED-for-propose.
