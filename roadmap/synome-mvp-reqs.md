# Synome-MVP Requirements (Phase 1)

**Status:** Draft
**Last Updated:** 2026-02-23
**Audience:** Engineering team building Synome-MVP

---

## What Synome-MVP Is

Synome-MVP is a shared database used by beacons and GovOps operators to coordinate Halo operations. It tracks **halo sleeves** (asset side), **halo units** (liability side, mapping to on-chain NFATs), **Core Halo entries** (legacy/static collateral), **risk framework configuration**, and **attestations** from an independent attestor.

Synome-MVP is not the long-term Synome. It does not need a graph database, inference engine, synlang, or cognitive layer. It is a structured, auditable, multi-writer database with a clear data model and simple read APIs.

---

## Data Model

Two Halo types (Term Halos and Core Halos), plus supporting entities:

### 1. Risk Framework

Published by Core Council via the `lpha-council` beacon. Contains the CRR equations and the data model that sleeves and units must conform to. Updated rarely. Read by `lpla-verify` to compute capital requirements.

### 2. Halo Sleeves

The asset-side container. A sleeve tracks what a Halo actually holds. Created and updated by `lpha-nfat`, with state transitions gated by attestations from `lpha-attest`.

**Sleeve lifecycle:**

```
CREATED ──► FILLING ──► OFFBOARDING ──► DEPLOYING ──► AT REST ──► UNWINDING ──► CLOSED
                                            │              │
                                      (no updates —       (periodic re-attestation
                                       intentional         from attest + nfat)
                                       obfuscation)
```

| State | What happens | Who writes |
|---|---|---|
| **Created** | Empty sleeve exists | nfat |
| **Filling** | Deposits swept in; sleeve assets updated with queued USDS | nfat |
| **Offboarding** | USDS → USDC → halo bank account; sleeve assets updated | nfat |
| **Deploying** | Black box. No sleeve updates. Assets being deployed into deals. Higher CRR applies. Duration capped by attest pre-authorization. | (no writes) |
| **At Rest** | Attested aggregate risk data written to sleeve. Periodic updates (e.g. quarterly) from attest + nfat confirming nominal status. | attest, nfat |
| **Unwinding** | Assets mature, USDC returns, converted to USDS. Sleeve updated. USDS moved to repayment. | nfat |
| **Closed** | All units redeemed, sleeve wound down | nfat |

**Key transitions requiring attestation:**

- **Filling → Deploying:** `lpha-attest` must first post a pre-deployment attestation (expected aggregate risk parameters, confirmation that legal docs check out, maximum deployment duration). Only then can `lpha-nfat` trigger the deploy phase.
- **Deploying → At Rest:** `lpha-attest` posts at-rest attestation (verifying aggregate risk parameters, confirming legal claims are in place per buybox/deal terms). Then `lpha-nfat` transitions the sleeve and writes the attested aggregate risk data.

**Sleeve data (what's stored):**

- Sleeve identifier and parent Halo
- Current state
- Asset summary (USDS amount during filling; aggregate risk data once at rest — exact fields TBD, must be flexible)
- Linked halo units
- Attestation references (which attestations gate which transitions)
- Timestamps for each state transition

### 3. Halo Units

The liability-side claim. Each unit maps 1:1 to an on-chain NFAT. Created by `lpha-nfat` when sweeping deposits from the facility queue.

**Unit lifecycle (Phase 1 — bullet loans only):**

```
CREATED ──► ACTIVE ──► REPAYMENT AVAILABLE ──► CLOSED
```

| State | What happens | Who writes |
|---|---|---|
| **Created** | Unit created alongside on-chain NFAT mint. Linked to a sleeve. | nfat |
| **Active** | Unit exists, underlying assets deployed via sleeve | (no writes needed) |
| **Repayment available** | USDS ready for Prime to claim | nfat |
| **Closed** | Prime burned NFAT, funds claimed | nfat |

**Unit data (what's stored):**

- Unit identifier
- On-chain NFAT reference (token ID, facility address, tx hash)
- Linked sleeve
- Principal amount
- Deal terms (APY, maturity date, payment schedule = `bullet` in Phase 1)
- Depositor Prime
- Current state
- Timestamps

### 4. Attestations

Standalone records written by `lpha-attest`. Referenced by sleeves but stored independently so there's a clean audit trail of what the attestor said and when.

**Types:**

| Type | When | Content |
|---|---|---|
| **Pre-deployment** | Before sleeve enters deploying | Expected aggregate risk parameters, legal confirmation, maximum deployment duration |
| **At-rest** | When deployment complete | Confirmed aggregate risk parameters, legal claims verified |
| **Periodic** | Per schedule (e.g. quarterly) | Re-attestation that sleeve status is nominal, any risk data updates |

**Note on aggregate risk data:** The exact fields for aggregate risk parameters are not yet defined. Synome-MVP must be flexible here — store structured JSON that conforms to a schema version, but don't hardcode specific risk fields. The risk framework (published by council) will define what fields are expected; the attestor fills them in.

### 5. Core Halo Entries

Core Halos are legacy or static collateral positions (e.g., Morpho vaults, SparkLend, JAAA, BlackRock BUIDL) that don't go through the Term Halo sleeve lifecycle. They are registered once by `lpha-council` and then remain mostly static.

**How they differ from Term Halos:**

| | Term Halo (Sleeves + Units) | Core Halo |
|---|---|---|
| **Lifecycle** | Full state machine (created → ... → closed) | Static entry, state doesn't change |
| **Risk data source** | Attestor provides aggregate risk data | Council sets a fixed risk model + data model; lpla-verify scrapes live data itself |
| **Data updates** | nfat + attest beacons update Synome | No regular Synome updates — verify pulls fresh data at read time |
| **Beacons involved** | nfat, attest, verify | council (setup), verify (reads) |

**Core Halo data (what's stored):**

- Entry identifier and parent Prime
- Collateral type and description
- On-chain address(es) where applicable
- Risk model reference (which risk framework equations apply)
- Data model reference (what data lpla-verify should collect — on-chain via RPC, or off-chain via financial APIs)
- Any static parameters set by council (e.g., haircuts, classification)

**How lpla-verify uses Core Halos:** For each Core Halo entry, lpla-verify reads the data model reference to know *what* to fetch and *where* to fetch it from (on-chain RPC for DeFi positions, financial APIs for assets like JAAA or BUIDL), then applies the risk model to compute CRR. Synome-MVP stores the configuration; lpla-verify fetches live data externally.

---

## Beacons and Access

| Beacon | Reads | Writes |
|---|---|---|
| **lpha-council** | — | Risk framework (equations + data model), Core Halo entries |
| **lpha-nfat** | Deal parameters, attestations | Sleeves (create, update, state transitions), Units (create, update) |
| **lpha-attest** | Sleeve state (to know when to attest) | Attestations |
| **lpla-verify** | Risk framework, sleeves, units, attestations, Core Halo entries | Nothing (read-only; computes CRR externally, fetches live data for Core Halos via RPC/APIs) |

**Human operators** (Core Council GovOps, Halo Ops) write through beacon tooling, not directly to the database. A CLI/admin escape hatch for emergencies is acceptable but should be logged distinctly.

---

## End-to-End Flows (What Must Work)

### Flow 1: Risk Framework Setup

```
Core Council ──(lpha-council)──► Synome: publish risk framework
                                         │
lpla-verify ◄───────────────────── reads risk framework
                                   + reads on-chain positions (from RPC, not Synome)
                                   = computes CRR
```

If missing: lpla-verify cannot compute CRR. No risk monitoring.

### Flow 2: NFAT Issuance (Sleeve + Unit Creation)

```
lpha-nfat: create Halo Sleeve (empty)
    │
    ▼
lpha-nfat: sweep deposit from facility queue
    ├─ on-chain: mint NFAT, move USDS
    └─ synome: create Halo Unit (linked to sleeve), update Sleeve assets
    (repeat for additional sweeps into same sleeve)
    │
    ▼
lpha-nfat: offboard (USDS → USDC → halo bank account)
    └─ synome: update Sleeve assets
```

If missing: No record linking on-chain NFATs to deal terms. Sleeve asset tracking lost.

### Flow 3: Attestation-Gated Deployment

```
lpha-attest: post pre-deployment attestation
    └─ synome: attestation stored (risk params, legal confirmation, max duration)
    │
    ▼
lpha-nfat: reads attestation, triggers deploy phase
    └─ synome: Sleeve → DEPLOYING (no further updates until at-rest)
    │
    ── black box (higher CRR, no sleeve updates) ──
    │
    ▼
lpha-attest: post at-rest attestation
    └─ synome: attestation stored (confirmed risk params, legal claims verified)
    │
    ▼
lpha-nfat: reads attestation, transitions sleeve
    └─ synome: Sleeve → AT REST, aggregate risk data written
```

If missing: No attestation gate means nfat beacon could deploy without independent verification. CRR calculations during deployment have no basis.

### Flow 4: Periodic Re-Attestation

```
lpha-attest: post periodic attestation (e.g. quarterly)
    └─ synome: attestation stored
lpha-nfat: update sleeve (nominal, risk data refresh)
    └─ synome: sleeve updated
```

If missing: Stale risk data on sleeves; CRR may drift from reality.

### Flow 5: Repayment and Closure

```
Underlying assets mature → USDC returns to halo PAU
    │
    ▼
lpha-nfat: convert USDC → USDS, update sleeve
    └─ synome: Sleeve → UNWINDING, assets updated
    │
    ▼
lpha-nfat: move USDS to repayment
    └─ synome: Sleeve updated, Unit(s) → REPAYMENT AVAILABLE
    │
    ▼
Prime burns NFAT on-chain
    └─ synome: Unit → CLOSED
    └─ synome: Sleeve → CLOSED (when all units closed)
```

If missing: No tracking of repayment status; Primes and lpla-verify can't see that funds are available.

---

## What Synome-MVP Must Do

- Store and serve the entities above (risk framework, sleeves, units, attestations) with full history
- Enforce that only authorized beacons/operators can write to their respective entity types
- Provide an audit trail: who wrote what, when, to which entity
- Expose a read API that supports:
  - Get entity by ID
  - Get latest state of an entity
  - List/filter entities by type, parent, state, time range
- Be operationally reliable: backups, restore procedure, uptime monitoring

## What Synome-MVP Must Not Do

- Compute CRR or any risk calculations (that's lpla-verify)
- Execute on-chain transactions (that's the beacons)
- Enforce business logic about state transitions (beacons own their own logic; Synome stores the results)
- Index on-chain data (beacons read on-chain state via RPC/indexers separately)

---

## Non-Goals and Overengineering Traps

| Trap | Why to avoid it |
|---|---|
| **Crypto-signed statement ledger** | Authenticated writes with audit logs are sufficient for Phase 1. The readers are our own beacons, not adversarial third parties. Design the schema to accommodate an optional signature field for later. |
| **Graph database / probabilistic mesh** | Synome-MVP is a structured database with four entity types. A relational DB or document store is fine. |
| **On-chain indexing as a dependency** | Beacons read on-chain state directly. Synome-MVP stores off-chain operational data only. |
| **Workflow orchestration** | No approval flows, task queues, or human inboxes. Beacons are autonomous; humans write through tools. |
| **Full UI product** | A CLI and/or simple admin interface is sufficient. Dashboards are Phase 2+. |
| **Generic analytics / data lake** | Synome-MVP serves beacons. Analytics can query it downstream but should not drive its design. |
| **Deep schema validation** | Validate required fields exist and types are sane. Don't embed business rules about valid CRR ranges or NFAT term constraints — that's beacon logic. |
| **Multi-payment NFAT schedules** | Phase 1 is bullet loans only (one maturity, one redemption). The schema should have a `payment_schedule` field that says `"bullet"` so it extends later, but don't build amortizing/periodic payment tracking now. |

---

## Flexibility Requirements

Some parts of the data model are not yet fully defined. Synome-MVP must accommodate this:

- **Aggregate risk data on sleeves:** The exact fields will be defined by the risk framework (published by council). Synome-MVP should store this as structured JSON conforming to a schema version, not as fixed columns.
- **Attestation content:** The attestor's data format may evolve. Same approach — versioned schema, flexible payload.
- **Additional entity metadata:** Sleeves and units may gain fields as operations mature. Use a data model that handles schema evolution without migrations blocking operations.

---

## Acceptance Tests

| # | Test | Pass criteria |
|---|---|---|
| 1 | **Risk framework flow** | Council publishes risk framework via lpha-council. lpla-verify reads it and computes CRR for a sample portfolio. |
| 2 | **Sleeve + unit creation** | lpha-nfat creates a sleeve, sweeps a deposit (creating a unit), and updates the sleeve. All records retrievable via API. |
| 3 | **Attestation-gated deployment** | lpha-attest posts pre-deployment attestation. lpha-nfat transitions sleeve to deploying. Verify that the transition is recorded and the attestation is linked. |
| 4 | **At-rest transition** | lpha-attest posts at-rest attestation. lpha-nfat transitions sleeve to at rest with aggregate risk data. lpla-verify can read the sleeve and compute CRR. |
| 5 | **Repayment and closure** | lpha-nfat updates sleeve through unwinding, marks units as repayment available, then closed. Full lifecycle visible in history. |
| 6 | **Core Halo flow** | Council registers a Core Halo entry (e.g., a Morpho vault) with risk model and data model references. lpla-verify reads the entry, fetches live position data from RPC, and computes CRR. |
| 7 | **Authorization enforcement** | An unauthorized writer is rejected. Authorized writers can only write to their permitted entity types. |

---

## Open Questions

1. **Aggregate risk data schema:** What fields does the risk framework need on at-rest sleeves? (Blocked on risk framework design — Synome-MVP team should build flexible JSON storage and not wait for this.)
2. **Attestation cadence:** Is quarterly the standard for Phase 1, or does it vary by Halo/asset type?
3. **Auth mechanism:** API keys per beacon, mTLS, or something else? (Engineering team should propose; does not need to be crypto signatures in Phase 1.)

---

## Links

- Phase 1 overview: [`phase-1-pragmatic-delivery.md`](./phase-1-pragmatic-delivery.md)
- NFAT specification: [`../smart-contracts/nfats.md`](../smart-contracts/nfats.md)
- Term Halo overview: [`../sky-agents/halo-agents/term-halo.md`](../sky-agents/halo-agents/term-halo.md)
- Beacon framework: [`../synomics/macrosynomics/beacon-framework.md`](../synomics/macrosynomics/beacon-framework.md)
