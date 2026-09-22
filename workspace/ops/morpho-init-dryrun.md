# MorphoSupply init dry-run — RH fork 9994663

**As of:** 2026-09-22 ~12:39 America/Bogota (COT)  
**Authority:** OWNER GO via Desk Lead — dry-run only.  
**Hard constraints honored:** NO propose · NO broadcast · proposal #2 untouched · eth_call / Tenderly sim / `debug_traceCall` only (agent nonce unchanged = 14).

**RPC:** ``Tenderly RH-fork RPC (see SETUP — do not commit private fork URLs)``

---

## Verdict

| Gate | Result |
|------|--------|
| **Init dry-run (cloneAndInit + initialize)** | **PASS** |
| Encoding (`abi.encode(morpho, MarketParams, supplyAmount)`) | **PASS** — marketId matches |
| Revert-selector decode (negative controls) | **PASS** |
| Chain mutation / mine | **NONE** |
| **S1 CLEARED-for-propose-after-#2** | **NO** — still no CLI `morpho-supply` + #2 Pending HOLD. Dry-run OK = **Ops init gate cleared for manual path post-#2 only**. |

---

## What was called (read-only / sim)

### 1) TierRegistry `isCounterpartyAllowed`

Registry: `0x4614f058920941A0a9a852e62485fbDE692D75E1`

| Address | Role | `isCounterpartyAllowed` |
|---------|------|-------------------------|
| `0x9D53d5E3bd5E8d4Cbfa6DB1ca238AEA02E651010` | Morpho Blue | **true** |
| `0xD625d488D552775D2867194C618B945E5dDfE097` | oracle (AAPL market) | **false** |
| `0x2BD3d5965B26B51814AC95127B2b80dD6CcC0fa1` | AdaptiveCurve IRM | **false** |
| `0x5fc5360D0400a0Fd4f2af552ADD042D716F1d168` | USDG (loan) | **false** |
| `0xaF3D76f1834A1d425780943C99Ea8A608f8a93f9` | AAPL (collateral) | **false** |
| `0x5B55E1Da361573CB0788e750038567D2569BE41d` | MorphoSupply template | false |
| `0xb06788F027268a9A06c3AD41a88559530D2E54b1` | StrategyFactory | false |
| `0xE075cc9e3F55007B6D7e63439AA4c8094B84288F` | Vault | false |
| `0x3E11F357De42Ae396fCA812db5f8FA1C576DC225` | Agent/proposer | false |

**Note:** Master-branch MorphoSupply natspec binds **only Morpho** as the adapter/singleton allowlist target; `oracle` / `irm` / `collateralToken` / `loanToken` are **not** required to be counterparties for MorphoSupply init (loanToken is checked vs `vault.asset()` only). Counterparty=false on oracle/IRM/USDG/AAPL did **not** block init — consistent with that design and with the successful sim below.

### 2) TierRegistry `isAdapterAllowed`

**Absent on this fork build.** Every `eth_call` to `isAdapterAllowed(address)` reverts with empty data `0x`. Selector `0xe219bbf6` is **not** present in TierRegistry bytecode; `isCounterpartyAllowed` (`0x51837392`) **is**. Deployed MorphoSupply template still embeds `MorphoNotAllowed(address,address)` (`0xc2be4011`) and resolved Morpho successfully in sim — init gate on this build is satisfied without a working `isAdapterAllowed` surface (Morpho already counterparty-true).

### 3) Pre-flight eth_calls

| Check | Result |
|-------|--------|
| `StrategyFactory.approvedTemplate(MorphoSupply)` | **true** |
| `vault.asset()` | USDG `0x5fc5360D…d168` |
| `vault.governor()` → `tierRegistry()` | `0x2f458502…119C` → TierRegistry above |
| `vault.isAgent(agent)` | **true** |
| `vault.owner()` | agent `0x3E11F357…C225` |
| Morpho `idToMarketParams(marketId)` | USDG / AAPL / oracle / IRM / 62.5% LLTV |
| Morpho `market(marketId).lastUpdate` | **non-zero** (market created) |
| `keccak256(abi.encode(MarketParams))` | `0xdeb4782d012d5fd3b24962538c2f6559049d70bda4dabd2e4212dacb96c28d45` **match** |

### 4) Encoded initData (dust)

```text
abi.encode(
  morpho = 0x9D53d5E3bd5E8d4Cbfa6DB1ca238AEA02E651010,
  MarketParams{
    loanToken:       0x5fc5360D0400a0Fd4f2af552ADD042D716F1d168,  // USDG
    collateralToken: 0xaF3D76f1834A1d425780943C99Ea8A608f8a93f9,  // AAPL
    oracle:          0xD625d488D552775D2867194C618B945E5dDfE097,
    irm:             0x2BD3d5965B26B51814AC95127B2b80dD6CcC0fa1,
    lltv:            625000000000000000                       // 62.5% WAD
  },
  supplyAmount = 1_000_000   // 1 USDG (6 decimals) dust
)
```

**initData (hex):**

```
0x0000000000000000000000009d53d5e3bd5e8d4cbfa6db1ca238aea02e651010
0000000000000000000000005fc5360d0400a0fd4f2af552add042d716f1d168
000000000000000000000000af3d76f1834a1d425780943c99ea8a608f8a93f9
000000000000000000000000d625d488d552775d2867194c618b945e5ddfe097
0000000000000000000000002bd3d5965b26b51814ac95127b2b80dd6ccc0fa1
00000000000000000000000000000000000000000000000008ac7230489e8000
0000000000000000000000000000000000000000000000000000000000000f4240
```

(224 bytes; flat ABI — MarketParams inlined as 5 words, no dynamic offset.)

### 5) Positive simulations (no mine)

Caller / `from` = agent `0x3E11F357De42Ae396fCA812db5f8FA1C576DC225`  
Target = StrategyFactory `0xb06788F027268a9A06c3AD41a88559530D2E54b1`  
Template = `0x5B55E1Da361573CB0788e750038567D2569BE41d`  
Vault = `0xE075cc9e3F55007B6D7e63439AA4c8094B84288F`

| Method | Result |
|--------|--------|
| `eth_call` `cloneAndInit(template, vault, agent, initData)` | **OK** → clone `0x7757B041f1f96b69F2b2aAA7E2057e3b1A3EfCAe` |
| `eth_call` `cloneAndInitDeterministic(..., salt)` | **OK** → clone `0x3388f8C0785729C033018b60B9DCb428343B85f0` |
| `estimateGas` `cloneAndInit` | **460191** |
| `tenderly_simulateTransaction` (`save` not persisted; status true) | **OK** · gasUsed `0x6e08d` · same return clone |
| `debug_traceCall` (callTracer) | **OK** · CREATE clone + `initialize` CALL success · no `error` |

Trace shape (abbrev): Factory STATICCALLs membership/registry → CREATE EIP-1167 proxy of MorphoSupply template → CALL `initialize(vault, proposer, initData)` on clone → success.

Agent nonce before/after: **14** (unchanged). Block observed ~67083158. Nothing mined.

---

## Negative controls — revert selectors decoded

Same factory path; only initData / args changed. Proves encoding + error surface.

| Case | Selector | Name |
|------|----------|------|
| `loanToken = WETH` (≠ vault asset) | `0x58ec95f2` | `LoanAssetMismatch()` |
| `supplyAmount = 0` | `0x2c5211c6` | `InvalidAmount()` |
| `morpho = address(0)` | `0xd92e233d` | `ZeroAddress()` |
| unapproved template (= Morpho addr) | `0x132ab367` + addr | `TemplateNotApproved(address)` |
| `proposer ≠ msg.sender` | `0x5b6a76bf` | `ProposerMustBeSender()` |

Known but **not** hit on happy path: `MorphoNotAllowed(address,address)` `0xc2be4011`, `MarketNotCreated()` `0x96e13529`.

---

## What this does **not** clear

1. **No CLI** — `sherwood strategy propose` still has no `morpho-supply` key; live path remains **manual** `StrategyFactory.cloneAndInit` + governor propose batches.  
2. **Proposal #2** still Pending / execute HOLD — no satellite propose until Settled.  
3. This dry-run covers **init only** (clone + `_initialize`). It does **not** simulate execute (USDG approve + supply) or settle.  
4. AAPL market remains near-idle (~0.04% util) — float sleeve, not yield chase.  
5. Do **not** treat S1 as full `CLEARED-for-propose` until #2 Settled + Ops/OWNER unlock for manual propose.

---

## Ops posture after this dry-run

- **Ops init gate:** **CLEARED** (encoding + fork `cloneAndInit`/`initialize` proven).  
- **S1 status label:** keep **conditional CLEARED for Ops** / note **init dry-run OK**; **not** `CLEARED-for-propose-after-#2`.  
- **Next (human/OWNER, post-#2 only):** manual clone with dust or sized `supplyAmount`, build execute `[USDG.approve(clone, amt), clone.execute()]` + settle `[clone.settle()]`, then propose — still no broadcast until unlocked.

Cookbook pointer: `ops/morpho-cl-cookbook.md` (Dry-run section).
