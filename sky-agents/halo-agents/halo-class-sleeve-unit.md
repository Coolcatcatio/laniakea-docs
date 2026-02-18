# Halo Architecture: Class, Sleeve, Unit

**Status:** Draft
**Last Updated:** 2026-02-12

---

## Overview

Halos organize capital into three layers: **Class**, **Sleeve**, and **Unit**. Each layer serves a distinct purpose, and the boundaries between them determine how infrastructure is shared, where risk is isolated, and what investors actually hold.

```
Halo Class (shared smart contract infra + default terms)
    |
    +-- ASSET SIDE: Halo Sleeves
    |   +-- Sleeve A (holds Loan X + Loan Y)
    |   +-- Sleeve B (holds Loan Z)
    |
    +-- LIABILITY SIDE: Halo Units
        +-- Unit 1 --> claim on Sleeve A
        +-- Unit 2 --> claim on Sleeve A
        +-- Unit 3 --> claim on Sleeve B
```

---

## Halo Class

A Halo Class is the **unit of smart contract infrastructure and default terms**. It defines the product family — what kinds of deals can happen, through what contracts, under what rules.

### What a Class Provides

| Component | Purpose |
|---|---|
| **PAU** | Single Controller + ALMProxy + RateLimits shared by all Units in the Class |
| **LPHA Beacon(s)** | `lpha-lcts` for Portfolio Classes; `lpha-nfat` + `lpha-attest` for Term Classes |
| **Legal Buybox** | Acceptable parameter ranges — duration, size, APY, asset types, counterparty requirements |
| **Queue Contract** | Where capital enters (Primes deposit here) |
| **Redeem Contract** | Where capital exits (Halo deposits returned funds here) |
| **Factory Template** | All Units deployed from the same pre-audited template |

### What a Class Defines

The Class sets the **default terms and constraints** that all Units and Sleeves within it must respect:

- **Parameter ranges** (the buybox): what durations, sizes, yields, and asset types are acceptable
- **Counterparty requirements**: which Primes can participate, what qualifications are needed
- **Recourse mechanisms**: what happens on default, what the Fortification Conserver can do
- **Operational rules**: how beacons operate, what rate limits apply, what reporting is required

Anything within the buybox can be executed autonomously by the LPHA beacon. Anything outside requires governance intervention.

### One Class, Many Products

A single Class can support multiple Sleeves and many Units — all sharing the same contracts and legal framework but offering different risk/return profiles. This is what makes Halos scalable: the expensive infrastructure (smart contracts, legal setup, audits, governance approval) is built once per Class, then reused across every Unit.

---

## Halo Sleeve

A Halo Sleeve is an **asset-side container** — a bankruptcy-remote boundary holding the actual assets that back one or more Units. Sleeves are where the real-world risk lives.

### Core Properties

| Property | Description |
|---|---|
| **Bankruptcy remoteness** | The sleeve is the isolation boundary. If assets in one sleeve fail, other sleeves are unaffected. |
| **Pari passu within** | All Units claiming on the same sleeve share losses equally (unless explicitly tranched) |
| **Fully isolated across** | Units on different sleeves have zero exposure to each other |
| **Blended for privacy** | Multiple assets can be blended in a single sleeve, preventing outsiders from inferring individual deal terms |
| **Whole assets** | Each asset sits entirely in one sleeve — assets are not split across sleeves |
| **Recursive** | A sleeve can hold Halo Units from other sleeves as its assets, enabling structured layering |

### Sleeve Lifecycle

Sleeves progress through defined phases, each with different transparency and capital requirements:

```
Created --> Filling --> Deploying --> At Rest --> Unwinding --> Closed
```

| Phase | What's Happening | Synome Visibility | CRR Impact |
|---|---|---|---|
| **Created** | Empty sleeve exists | Full | None |
| **Filling** | NFATs swept in; sleeve holds USDS earning agent rate | Full transparency | Low |
| **Deploying** | Capital offboarded to real-world assets | Obfuscated (Schrodinger's risk) | **High** |
| **At Rest** | Fully deployed; attestor has confirmed risk characteristics | Attested risk profile (not individual borrower details) | Medium |
| **Unwinding** | Assets returning; Halo funds Redeem Contract | Transitional | -- |
| **Closed** | All Units redeemed; sleeve wound down | Archived | -- |

The high CRR during the deploying phase creates an economic incentive to minimize the obfuscated period — Halos want to get through deployment quickly and reach the lower at-rest CRR. This balances borrower privacy against capital efficiency without mandating specific behavior.

### Two-Beacon Deployment Gate

Neither beacon can trigger deployment alone:

1. **lpha-attest** (independent Attestor) posts a risk attestation into the Synome
2. Only then can **lpha-nfat** transition the sleeve from filling to deploying

This separation ensures independent validation before capital leaves the on-chain boundary.

### Why Sleeves Exist

Without sleeves, the isolation boundary would be either the Class (too broad — one bad deal contaminates everything) or the Unit (impractical — every individual NFAT would need its own legal entity and bank account). Sleeves sit in between: they group related assets for privacy and operational efficiency while maintaining meaningful bankruptcy remoteness between groups.

---

## Halo Unit

A Halo Unit is a **liability-side claim** — what the Halo owes to the holder. Each Unit maps to a specific Sleeve, giving the holder a claim on the assets in that sleeve.

### Two Token Standards

| | Portfolio (LCTS) | Term (NFAT) |
|---|---|---|
| **What the Unit is** | Shares in a pooled position | An individual ERC-721 token representing a bespoke deal |
| **Fungibility** | Fungible within the pool | Non-fungible — each NFAT has unique terms |
| **Terms** | Same for all participants | Bespoke per deal (within buybox) |
| **Transferability** | Non-transferable (internal accounting) | Transferable (optionally whitelist-restricted) |
| **What varies per Unit** | Seniority, yield, capacity, queue config | Duration, size, APY, counterparty, specific conditions |

### What a Unit Represents

A Unit is a **claim on a sleeve**, not a claim on a specific asset. The holder's exposure is to the blended contents of the sleeve, not to any individual loan or position within it. This is the privacy mechanism: even if you hold a Unit, you know your own terms (APY, duration, size) but cannot determine the individual terms of the underlying assets — only the blended risk characteristics as attested by the Attestor.

### Unit-to-Sleeve Mapping

The mapping between Units and Sleeves is flexible:

| Pattern | Description | Use Case |
|---|---|---|
| **1 Unit : 1 Sleeve** | Single deal, single container | Simple bilateral arrangement |
| **Many Units : 1 Sleeve** | Multiple NFATs backed by the same blended collateral | Privacy protection — individual terms can't be inferred from NFAT data |
| **Recursive** | A Sleeve holds Units from other Sleeves as assets | Structured products, tranching across sleeves |

In the simplest case (1:1:1 — one Unit, one Sleeve, one asset), the effect is identical to per-deal isolation. The sleeve structure adds flexibility without removing the simple case.

---

## How the Layers Interact

### Capital Flow

```
Prime deposits sUSDS
        |
        v
  Queue Contract  (Class-level)
        |
        | lpha-nfat claims from queue
        v
  Halo Sleeve  (asset side -- holds the capital)
        |
        | NFAT minted
        v
  Halo Unit  (liability side -- Prime holds this)
```

At redemption, the flow reverses: assets return to the sleeve, the Halo funds the Redeem Contract (Class-level), and the Unit holder burns their NFAT to claim.

### What Each Layer Controls

| Decision | Layer |
|---|---|
| What contracts are used? | **Class** |
| What parameter ranges are acceptable? | **Class** (buybox) |
| What rate limits apply? | **Class** (PAU) |
| Which beacon operates? | **Class** |
| Where is the bankruptcy-remote boundary? | **Sleeve** |
| Who bears losses if assets fail? | **Sleeve** (pari passu across Units on the same sleeve) |
| What blended risk profile do assets have? | **Sleeve** (via attestor) |
| What specific terms does the investor have? | **Unit** |
| Who holds the claim? | **Unit** |
| What can be transferred or traded? | **Unit** |

### Boundaries Summary

```
CLASS boundary = infrastructure sharing
                 (same contracts, same beacon, same legal framework)

SLEEVE boundary = risk isolation
                  (bankruptcy remoteness, loss containment, privacy)

UNIT boundary = individual claim
                (specific terms, specific holder, transferable position)
```

---

## Examples

### Example 1: Portfolio Halo — Tranched CLO

```
Halo Class: CLO Tranched
  (PAU + lpha-lcts + legal buybox for CLO assets)
  |
  +-- Sleeve alpha: Pool of CLO tranches
  |     |
  |     +-- Unit: Senior Tranche (lower yield, first claim on losses)
  |     +-- Unit: Junior Tranche (higher yield, absorbs losses first)
  |
  +-- Sleeve beta: Different CLO pool
        |
        +-- Unit: Single position
```

Senior and Junior Units on Sleeve alpha are pari passu on the sleeve's assets (with waterfall priority defined by tranche terms). If Sleeve beta's assets default, Sleeve alpha is unaffected.

### Example 2: Term Halo — Blended Lending Facility

```
Halo Class: Senior Secured Facility
  (PAU + lpha-nfat + lpha-attest + buybox: 6-24mo, 8-15% APY)
  |
  +-- Sleeve alpha: Loan A + Loan B (blended)
  |     |
  |     +-- NFAT #1: 25M, 6mo, 10% APY, held by Spark
  |     +-- NFAT #2: 50M, 12mo, 11% APY, held by Grove
  |
  +-- Sleeve beta: Loan C + Loan D (blended)
        |
        +-- NFAT #3: 30M, 18mo, 12% APY, held by Spark
        +-- NFAT #4: 15M, 9mo, 9% APY, held by Keel
```

NFAT holders #1 and #2 share fate on Sleeve alpha (pari passu). NFAT holders #3 and #4 share fate on Sleeve beta. The two sleeves are fully isolated. None of the four NFAT holders can infer the individual terms of Loans A-D — only the blended risk characteristics attested by the Attestor.

### Example 3: Simple 1:1:1

```
Halo Class: Bilateral Facility
  |
  +-- Sleeve gamma: Single bond position
        |
        +-- NFAT #5: 100M, 12mo, held by Grove
```

One Unit, one Sleeve, one asset. The sleeve structure adds no overhead in this case — it degenerates to straightforward per-deal isolation.

---

## Legal Mapping

| Halo Concept | BVI SPC Equivalent | Delaware Equivalent |
|---|---|---|
| **Halo Class** | The SPC entity itself | The Series LLC parent |
| **Halo Sleeve** | Segregated portfolio (statutory ring-fencing under BVI BCA s.146) | Individual series (untested in bankruptcy) |
| **Halo Unit** | Share/interest within a portfolio | Membership interest in a series |

BVI SPCs provide materially stronger sleeve-level isolation than Delaware Series LLCs — the BVI statutory segregation has been court-tested, while Delaware series isolation has not.

At scale (100+ concurrent Units), a hybrid approach applies: statutory isolation (separate portfolio) for high-value sleeves, contractual isolation (grouped portfolio with limited recourse clauses) for smaller ones.

---

## Related Documents

| Document | Relationship |
|---|---|
| `agent-type-halos.md` | Halos as a Synomic Agent type; spectrum from minimal to complex |
| `portfolio-halo.md` | Portfolio Halo business overview (LCTS-based Classes) |
| `term-halo.md` | Term Halo business overview (NFAT-based Classes) |
| `../smart-contracts/lcts.md` | LCTS token standard — queue mechanics for Portfolio Units |
| `../smart-contracts/nfats.md` | NFAT token standard — bespoke deal mechanics for Term Units |
