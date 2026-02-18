# Laniakea Phase 1: Pragmatic Delivery

**Status:** Draft
**Last Updated:** 2026-02-12

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Sentinels and Beacons](#sentinels-and-beacons)
3. [Diamond PAU Deployment](#1-diamond-pau-deployment)
4. [Synome-MVP](#2-synome-mvp)
5. [Verify Beacon](#3-verify-beacon)
6. [Relay Beacon](#4-relay-beacon)
7. [Core Halos and Legacy Cleanup](#5-core-halos-and-legacy-cleanup)
8. [Configurator Unit](#6-configurator-unit)
9. [NFAT Smart Contracts](#7-nfat-smart-contracts)
10. [NFAT Beacon](#8-nfat-beacon)
11. [Report Beacon](#9-report-beacon)
12. [Collateral Beacon](#10-collateral-beacon-speculative)
13. [Council Beacon](#11-council-beacon)
14. [Term Halo Legal](#12-term-halo-legal)
15. [SOFR Hedging](#13-sofr-hedging)
16. [Substages](#substages)

---

## Executive Summary

Phase 1 is pragmatically focused on delivering a minimal viable infrastructure for automated capital deployment. **Phase 1 continues using the existing manual settlement process**; formalized monthly settlement with lpla-checker begins in Phase 2, and daily settlement is the Phase 3 target.

Seven deliverables in sequence order:

1. **Diamond PAU Deployment** — Upgrade to Diamond PAU architecture (EIP-2535)
2. **Synome-MVP** — Operational database for risk parameters and NFAT records
3. **MVP Beacons** — Five to six low-power beacons for monitoring, reporting, execution, and governance (lpha-collateral is speculative)
4. **Core Halos and Legacy Cleanup** — Standardize legacy assets as Core Halos, wind down the rest
5. **Configurator Unit** — Simplest form enabling spell-less operations
6. **NFAT Smart Contracts** — Facility and Redeemer contracts for Term Halos
7. **Term Halo Legal** — Legal infrastructure enabling autonomous NFAT operations

**Bootstrap note:** Starting in Phase 1, Sky governance manually allocates liability duration matching (top-down). Sealed-bid OSRC and Duration auctions begin later once Prime-side `stl-base` is deployed.

**Settlement note:** Phase 1 does not formalize or automate settlement. The existing manual settlement process continues. lpla-verify monitors positions and calculates CRRs but does not track settlement progress — that capability arrives with lpla-checker in Phase 2.

---

## Sentinels and Beacons

To reduce overload of the term "sentinel," Phase 1 introduces a broader taxonomy: **beacons**.

### Beacon Taxonomy

Beacons are autonomous operational components. They vary along two axes:

| Axis | Low | High |
|------|-----|------|
| **Power** | Programs (deterministic, rule-based) | AI (adaptive, learning) |
| **Authority** | Controls independent entities (Halos, external systems) | Synome or smart contract capabilities (protocol-level) |

**Sentinels** are a subset of beacons — specifically, high-power (AI) beacons that will eventually manage Prime and Halo operations autonomously.

### Phase 1: Low-Power Beacons

Phase 1 deploys **low-power beacons** (programs, not AI) to enable Core Halo and NFAT operations at smaller scale:

| Beacon              | Type                      | Purpose                                                      |
| ------------------- | ------------------------- | ------------------------------------------------------------ |
| **lpla-verify**     | Low Power, Low Authority  | Monitor positions, calculate CRRs, generate alerts           |
| **lpha-relay**      | Low Power, High Authority | Execute PAU transactions with rate limits                    |
| **lpha-nfat**       | Low Power, High Authority | Manage NFAT Facility lifecycle, write NFAT records to Synome |
| **lpha-report**     | Low Power, High Authority | Post 24h Prime performance summaries to Synome               |
| **lpha-collateral** | Low Power, High Authority | Upload Core Halo / legacy RWA data to Synome (speculative)   |
| **lpha-council**    | Low Power, High Authority | Core Council toolkit for risk equations and report formats   |

These MVP beacons provide the operational foundation. lpla-verify is extended into the full lpla-checker in Phase 2 (adding settlement tracking). As scale increases, high-power (AI) sentinels will replace or augment beacons with adaptive decision-making.

### Beacon Categories

**Read-only (LPLA):**
- lpla-verify — observes positions, calculates CRRs, generates alerts (no settlement tracking — that arrives with lpla-checker in Phase 2)

**Reporters (LPHA):**
- lpha-report — writes Prime performance summaries to Synome
- lpha-collateral — writes Core Halo / legacy data to Synome (speculative — may not be needed)
- lpha-nfat — writes NFAT records to Synome (also executes)

**Guardians (LPHA):**
- lpha-relay — executes PAU transactions
- lpha-nfat — executes NFAT lifecycle operations

**Governance tooling (LPHA):**
- lpha-council — Core Council interface for updating Synome

---

## 1. Diamond PAU Deployment

Phase 1 deploys the **Diamond PAU** architecture (EIP-2535 diamond proxy pattern) replacing the legacy PAU pattern. Diamond PAUs provide modular, upgradeable functionality without full contract redeployment.

### Diamond PAU Architecture

| Component | Purpose |
|-----------|---------|
| **Diamond Proxy** | Single entry point; delegates to facets based on function selector |
| **Action Facets** | Modular contract pieces implementing specific actions |
| **DiamondCut Facet** | Adds/removes/replaces facets via governance |
| **DiamondLoupe Facet** | Introspection — lists available facets and functions |
| **RateLimits** | Linear replenishment with configurable max and slope (unchanged) |

### Benefits Over Legacy PAU

| Aspect | Legacy PAU | Diamond PAU |
|--------|------------|-------------|
| **Adding actions** | Requires full controller redeployment | Add new facet only |
| **Upgrading logic** | Replace entire controller | Replace single facet |
| **Contract size** | Limited by 24KB contract limit | Unlimited (split across facets) |
| **Factory deployment** | Manual per-Prime | Standardized factory for all Primes |

### Phase 1 Action Facets

**Core Facets:**
- `NfatDepositFacet` — Deposit into NFAT Facility queues
- `NfatWithdrawFacet` — Withdraw from queues before deal execution
- `CoreHaloFacet` — Deploy into Core Halo positions

**Migration Facets:**
- `LegacyMigrationFacet` — Wind down legacy positions during transition

### Migration Path

Each Prime migrates from legacy PAU to Diamond PAU:
1. Deploy Diamond PAU with initial facet set
2. Migrate positions from legacy ALMProxy to new Diamond
3. Grant cBEAMs to GovOps for new Diamond
4. Wind down legacy PAU once migration complete

---

## 2. Synome-MVP

Synome-MVP is the operational database that stores risk parameters and NFAT state. It accepts signed statements from authorized parties and serves as the source of truth for the MVP beacons.

### How It Works

```
┌─────────────────────────────────────────────────────────────────┐
│                         INPUTS                                   │
│                                                                  │
│  ┌───────────────────┐         ┌───────────────────┐            │
│  │ Core Council      │         │ Operational       │            │
│  │ GovOps            │         │ GovOps (Halo)     │            │
│  │                   │         │                   │            │
│  │ Signed statements │         │ NFAT deal params  │            │
│  │ for risk params   │         │ for lpha-nfat     │            │
│  └─────────┬─────────┘         └─────────┬─────────┘            │
│            │                             │                       │
│            └──────────────┬──────────────┘                       │
│                           ▼                                      │
│                  ┌─────────────────┐                             │
│                  │   SYNOME-MVP    │                             │
│                  │                 │                             │
│                  │ • Risk params   │                             │
│                  │ • NFAT records  │                             │
│                  │ • Position data │                             │
│                  └────────┬────────┘                             │
│                           │                                      │
└───────────────────────────┼──────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│                        OUTPUTS                                   │
│                                                                  │
│  ┌───────────────────┐         ┌───────────────────┐            │
│  │   lpla-verify    │         │    lpha-nfat      │            │
│  │                   │         │                   │            │
│  │ Reads risk params │         │ Reads NFAT params │            │
│  │ for CRR calcs     │         │ for deal execution│            │
│  └───────────────────┘         └───────────────────┘            │
└─────────────────────────────────────────────────────────────────┘
```

### Data Flows

| Source | Data | Consumer |
|--------|------|----------|
| Core Council GovOps | Signed risk parameter updates | lpla-verify (for CRR calculations) |
| Operational GovOps (Halo) | NFAT deal parameters | lpha-nfat (for deal execution) |
| lpha-nfat | Issued NFAT records | Synome-MVP (permanent record) |

### What Synome-MVP Stores

- **Risk parameters** — CRR weights, thresholds, limits (signed by Core Council GovOps)
- **NFAT records** — Parameters for each issued NFAT (amount, APY, maturity, counterparty)
- **Position data** — Current state of Prime/Halo positions

---

## 3. Verify Beacon

The Verify Beacon (lpla-verify) is the Phase 1 monitoring system for position verification and risk alerting.

**Purpose:** Monitor positions, calculate CRRs, and generate alerts (read-only reporting)

| Responsibility | Description |
|----------------|-------------|
| **Position Verification** | Verify on-chain positions match expected state |
| **CRR Calculation** | Calculate Capital Ratio Requirements using risk params from Synome-MVP |
| **Alert Generation** | Flag positions approaching risk thresholds |

**Inputs:** On-chain position data, price feeds, risk parameters from Synome-MVP

**Outputs:** CRR calculations, verification results, alerts

> **Note:** lpla-verify is LPLA (Low Power, Low Authority) because it only reads and reports — no execution authority. Settlement tracking is added when lpla-verify is extended into the full lpla-checker in Phase 2.

---

## 4. Relay Beacon

The Relay Beacon (lpha-relay) is the execution system that deploys assets via PAU into NFATs and legacy assets (Core Halos).

**Purpose:** Execute PAU transactions with rate limits and relay role

| Responsibility | Description |
|----------------|-------------|
| **Rate-Limited Execution** | Submit transactions within governance-approved rate limits |
| **Relay Role** | Act as authorized executor for PAU operations |
| **NFAT Deployment** | Deploy capital into NFAT Facilities |
| **Legacy Deployment** | Deploy capital into Core Halo legacy assets |
| **Queue Processing** | Process pending operations in priority order |
| **Failure Handling** | Retry failed transactions, escalate persistent failures |

**Inputs:** Execution requests from governance/operations, rate limit parameters from Configurator

**Outputs:** Executed transactions, execution logs

---

## 5. Core Halos and Legacy Cleanup

Before deploying Configurator, current Prime exposures must be reduced and simplified. Legacy assets either become **Core Halos** or are wound down.

### Why Cleanup Matters

Each additional exposure creates complexity:

| Impact Area | Complexity Cost |
|-------------|-----------------|
| **Configurator** | More allocation targets = more cBEAM surface area to manage |
| **MVP Beacons** | More exposures = more data to scrape, process, and monitor |
| **Risk Framework** | More positions = more CRR calculations, more edge cases |
| **Operations** | More integrations = more failure modes to handle manually |

### Core Halos

**Core Halos** are legacy assets that are retained and standardized as Halo Units under Core Council maintenance:

| Aspect | Description |
|--------|-------------|
| **What they are** | Existing Prime allocations worth keeping (e.g., Spark, established vault positions) |
| **How they work** | Wrapped as Halo Units with standardized interfaces |
| **Governance** | Halo Unit Artifacts describe properties, parameters, and oracle data (no dedicated PAU or smart contracts) |
| **Integration** | lpha-relay deploys into Core Halos using the same relay infrastructure as NFATs |

**Initial scope:** Core Halos cover all assets that aren't Term Halos — including Morpho vaults, SparkLend, and other DeFi integrations. These assets can later transition into standard Halos controlled by their respective Primes as the infrastructure matures.

```
Legacy Asset Decision Tree:

┌─────────────────────────────────────────┐
│         LEGACY PRIME EXPOSURE           │
└──────────────────┬──────────────────────┘
                   │
                   ▼
        ┌─────────────────────┐
        │  Worth retaining?   │
        └──────────┬──────────┘
                   │
         ┌─────────┴─────────┐
         │                   │
         ▼                   ▼
   ┌───────────┐       ┌───────────┐
   │    YES    │       │    NO     │
   └─────┬─────┘       └─────┬─────┘
         │                   │
         ▼                   ▼
   ┌───────────┐       ┌───────────┐
   │ Wrap as   │       │ Wind down │
   │ Core Halo │       │ position  │
   └───────────┘       └───────────┘
```

### Cleanup Targets

- **Promote to Core Halo**: Valuable legacy assets standardized as Halo Units
- **Wind down**: Experimental, low-value, or redundant allocations
- **Consolidate**: Redundant exposures merged into fewer, larger positions
- **Document**: Each Core Halo's data requirements for MVP beacons

### Acceptance Criteria

Primes enter Phase 1 with:
- **Core Halos** — Legacy assets wrapped as standardized Halo Units
- **No orphan exposures** — Everything either becomes a Core Halo or is wound down
- **Consistent interfaces** — All remaining positions accessible via standard PAU actions

---

## 6. Configurator Unit

The Configurator enables GovOps teams to manage Prime operations without requiring Sky Spells for routine changes.

### How It Works

The Configurator uses a **two-tier access model**:

```
┌─────────────────────────────────────────────────────┐
│  aBEAMs (Core Council)                              │
│  • Add inits (pre-approved configs) via timelock    │
│  • Grant cBEAMs to GovOps teams                     │
│  • Remove permissions instantly (emergency)         │
└──────────────────────┬──────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────┐
│  Configurator Unit                                  │
│  (BEAMTimeLock → BEAMState → Configurator)         │
└──────────────────────┬──────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────┐
│  cBEAMs (GovOps Teams)                              │
│  • Set rate limits using approved inits             │
│  • Increase rate limits (max 25% per 18h via SORL) │
│  • Decrease rate limits (always instant)            │
│  • Set relayer/freezer addresses                    │
└──────────────────────┬──────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────┐
│  PAU (Controller + ALMProxy + RateLimits)           │
└─────────────────────────────────────────────────────┘
```

**Key concepts:**

| Term | Meaning |
|------|---------|
| **aBEAM** | Admin BEAM — Core Council role that manages inits and grants cBEAMs (via 14-day timelock) |
| **cBEAM** | Configurator BEAM — operational role held by GovOps for specific PAUs |
| **Init** | Pre-approved rate limit or controller action that GovOps can activate |
| **Accordant** | A GovOps is "accordant" to a PAU when they hold its cBEAM |
| **SORL** | Second-Order Rate Limit — constrains how fast rate limits can increase (25% per 18h) |

**Typical flow:**

1. Core Council creates inits for a new allocation target (14-day timelock)
2. Core Council grants cBEAM to GovOps for that PAU (14-day timelock)
3. GovOps activates rate limits using the approved inits
4. GovOps increases limits gradually via SORL (25% every 18 hours)
5. GovOps can decrease limits instantly at any time

**Safety properties:**

- Additions require 14-day timelock (time to detect problems)
- Removals are instant (emergency response)
- Rate limit increases are gradual (SORL bounds damage from compromise)
- Decreases are always allowed (can always reduce exposure)

---

## 7. NFAT Smart Contracts

Two contracts enable Term Halos:

### NFAT Facility

The Facility manages the complete NFAT lifecycle:

| Function | Description |
|----------|-------------|
| **Queue** | Holds Prime capital awaiting deal execution |
| **Claim** | lpha-nfat claims capital from queue when deal is struck |
| **Mint** | Issues ERC-721 NFAT representing the claim |
| **Deploy** | Transfers funds to RWA endpoint via PAU |

### NFAT Redeemer

The Redeemer processes NFAT redemptions:

| Function | Description |
|----------|-------------|
| **Receive** | Accepts returned funds from RWA endpoint |
| **Notify** | Signals NFAT holder that redemption is available |
| **Redeem** | NFAT holder presents token, receives funds, NFAT burns |

### Integration

- Facility and Redeemer connect via shared PAU
- lpha-nfat holds pBEAM for Facility operations
- All state changes logged to Synome

---

## 8. NFAT Beacon

The NFAT Beacon (lpha-nfat) manages the NFAT Facility lifecycle — from queue sweeping to RWA offboarding.

**Purpose:** Operate NFAT Facilities end-to-end

| Responsibility | Description |
|----------------|-------------|
| **Queue Sweeping** | Sweep assets from NFAT Facility queue when deals are ready |
| **NFAT Issuance** | Issue ERC-721 NFATs representing claims |
| **Redemption Funding** | Fund the NFAT redemption system when funds return |
| **RWA Offboarding** | Offboard funds to RWA endpoints |
| **Record Keeping** | Write issued NFAT parameters to Synome-MVP |

**Inputs:** Deal parameters from Synome-MVP (set by Operational GovOps), Facility queue state, RWA endpoint status

**Outputs:** Minted NFATs, deployment transactions, redemption funding, NFAT records to Synome-MVP

---

## 9. Report Beacon

The Report Beacon (lpha-report) posts Prime performance summaries to the Synome on a 24-hour cycle.

**Purpose:** Summarize how each Prime fulfilled its CRRs and encumbrance ratio since the last Synome update

| Responsibility | Description |
|----------------|-------------|
| **CRR Fulfillment** | Report whether Prime met its Capital Ratio Requirements |
| **Encumbrance Ratio** | Report Prime's encumbrance status |
| **24h Cycle** | Post summary aligned with Synome update cadence |
| **Historical Record** | Maintain performance history in Synome |

**Inputs:** Prime position data, CRR calculations from lpla-verify, encumbrance data

**Outputs:** 24h performance summaries written to Synome-MVP

---

## 10. Collateral Beacon (Speculative)

The Collateral Beacon (lpha-collateral) uploads data for Core Halos and legacy RWA positions.

> **Note:** This beacon is speculative and may not be needed depending on how Core Halo data flows are structured.

**Purpose:** Report Core Halo and legacy RWA data to Synome

| Responsibility | Description |
|----------------|-------------|
| **Core Halo Data** | Upload position data for legacy assets wrapped as Core Halos |
| **Legacy RWA** | Report relevant data for legacy real-world asset positions |
| **Format Compliance** | Follow report formats specified by lpha-council |

**Inputs:** Core Halo position state, legacy RWA data sources

**Outputs:** Collateral data written to Synome-MVP

---

## 11. Council Beacon

The Council Beacon (lpha-council) is the Core Council toolkit for managing Synome configuration.

**Purpose:** Enable Core Council to update risk equations and specify report formats

| Responsibility | Description |
|----------------|-------------|
| **Risk Equations** | Update equations in the Synome risk framework |
| **Report Formats** | Specify data formats for lpha-collateral and lpha-nfat reports |
| **Configuration Management** | Maintain Synome operational parameters |

**Inputs:** Core Council signed updates

**Outputs:** Updated risk equations and report format specifications in Synome-MVP

> **Note:** lpha-council is governance tooling, not an autonomous beacon. It provides the interface through which Core Council configures the operational Synome.

---

## 12. Term Halo Legal

The legal infrastructure for Term Halos must be established before NFAT Facilities can operate. This framework enables lpha-nfat to execute deals autonomously within governance-approved bounds.

### Design Principles (Inspired by Portfolio Halos)

| Principle | Application to Term Halos |
|-----------|----------------------------------|
| **Default Ownership to Sky** | Fortification Conserver can assume control of NFAT Facility assets if legal intervention is needed |
| **Standardized Structures** | Buybox templates enable rapid deal execution without per-deal legal negotiation |
| **Pre-signed Integration** | Primes and counterparties pre-sign agreements covering the full buybox parameter range |

### The Buybox Model

Each NFAT Facility defines a **buybox** — the acceptable parameter ranges for deals:

| Parameter | Example Range |
|-----------|---------------|
| **Duration** | 6-24 months |
| **Size** | 5M-100M per NFAT |
| **APY** | 8-15% |
| **Counterparties** | Approved Primes only |
| **Asset Types** | Senior secured loans, investment-grade bonds |

Deals within the buybox can be executed by lpha-nfat without additional governance approval. Deals outside the buybox require governance intervention.

### Legal Isolation

Each NFAT represents a claim on a distinct **Halo Unit** — legally and operationally isolated:

- If one NFAT's underlying deal fails, other NFATs in the facility are protected
- Each Halo Unit functions as a serialized LLC equivalent
- Recourse is limited to the specific Unit's assets

### Governance Artifacts

| Artifact | Contents |
|----------|----------|
| **Halo Artifact** | Overall governance, buybox definitions, recourse mechanisms, migration procedures |
| **Unit Artifact** | Per-NFAT operational parameters, legal recourse documentation, deal terms |

### Deliverables

1. **Buybox Template** — Reusable legal framework defining acceptable deal parameters
2. **Pre-signed Agreements** — Standard agreements Primes sign to participate in NFAT Facilities
3. **Recourse Mechanisms** — Procedures for Fortification Conserver intervention
4. **Artifact Templates** — Governance documentation for Halo and Unit Artifacts

---

## 13. SOFR Hedging

Primes deploying into NFATs with duration must manage interest rate risk. When using the ALDM (Asset-Liability Duration Matching) system for duration matching, Primes have two options:

| Option | Description |
|--------|-------------|
| **Fully Hedged** | Maintain a hedged interest rate position that provides variable yield based on SOFR movements |
| **SOFR Plus Terms** | Structure NFAT positions with SOFR plus spread pricing (floating rate) |

### Why This Matters

Fixed-rate NFATs with duration create interest rate exposure. If SOFR rises, the Prime holds a below-market position. The duration matching system assumes either:

1. The Prime has hedged this risk externally (swaps, futures), receiving variable SOFR exposure that offsets fixed NFAT duration, OR
2. The NFAT itself is priced as SOFR + spread, so yield floats with the benchmark

### Validation

lpla-verify validates that Primes deploying into duration NFATs have declared either:
- Hedge positions covering the interest rate exposure
- SOFR plus terms on the underlying NFAT

Primes without valid hedging or floating-rate terms cannot deploy into duration NFATs via the ALDM system.

---

## Substages

Phase 1 is delivered in seven sequential substages:

| Substage | Name | Key Outcome |
|----------|------|-------------|
| **1.0** | Planning | Stakeholder alignment, Prime cohort identification |
| **1.1** | Diamond PAU Deployment | Diamond PAUs deployed for first-cohort Primes |
| **1.2** | Operational Infrastructure | Synome-MVP, lpla-verify, lpha-relay operational |
| **1.3** | Legacy Cleanup & Core Halos | Legacy assets standardized or wound down |
| **1.4** | Configurator Deployment | Spell-less Prime operations enabled |
| **1.5** | First Term Halo | Halo1 live with end-to-end NFAT flow validated |
| **1.6** | Term Halo Cohort | Halo2-Halo6 deployed and accepting NFAT deployments |

---

### Phase 1.0: Planning

**Objective:** Establish target timeline for full Phase 1 delivery

- Align all stakeholders on scope and dependencies
- Identify first cohort of Primes participating in initial NFAT deployments
- Confirm Term Halo partners and onboarding order

---

### Phase 1.1: Diamond PAU Deployment

**Objective:** Deploy Diamond PAU architecture for first-cohort Primes

| Task                                 | Description                                          |
| ------------------------------------ | ---------------------------------------------------- |
| **Deploy Diamond PAU factory**       | Standardized factory for Diamond PAU deployment      |
| **Deploy first-cohort Diamond PAUs** | Diamond PAUs for Primes joining initial NFAT rollout |
| **Migrate from legacy PAUs**         | Migrate positions and wind down legacy controllers   |

---

### Phase 1.2: Operational Infrastructure

**Objective:** Deploy infrastructure enabling Prime deployment into Core Halos

| Task                      | Description                                                |
| ------------------------- | ---------------------------------------------------------- |
| **Deploy Synome-MVP**     | Operational database for risk parameters and position data |
| **Deploy Verify Beacon**  | lpla-verify for CRR calculations and position monitoring   |
| **Deploy Relay Beacon**   | lpha-relay for rate-limited PAU operations                 |

These components enable Primes to deploy into Core Halos with proper monitoring and execution infrastructure, even before Configurator removes the spell requirement. Settlement tracking is added in Phase 2 with the full lpla-checker.

---

### Phase 1.3: Legacy Cleanup & Core Halos

**Objective:** Complete legacy cleanup and standardize retained assets

| Task | Description |
|------|-------------|
| **Wind down remaining legacy** | Close out exposures not becoming Core Halos |
| **Standardize Core Halos** | Wrap retained legacy assets as Halo Units |
| **Document artifacts** | Ensure all Core Halos have complete Halo Unit Artifacts (properties, parameters, oracle data) |

---

### Phase 1.4: Configurator Deployment

**Objective:** Enable spell-less Prime operations

| Task                                 | Description                                              |
| ------------------------------------ | -------------------------------------------------------- |
| **Deploy Configurator Unit**         | BEAMTimeLock → BEAMState → Configurator stack            |
| **Register Core Halos**              | Add Core Halo allocation targets to Configurator         |
| **Grant cBEAMs**                     | Provision GovOps teams for their respective PAUs         |
| **Prime deployment into Core Halos** | All Primes can now deploy into Core Halos without spells |

---

### Phase 1.5: First Term Halo

**Objective:** Deploy and validate first Term Halo

| Task                            | Description                                                       |
| ------------------------------- | ----------------------------------------------------------------- |
| **Deploy NFAT Smart Contracts** | Facility and Redeemer contracts                                   |
| **Deploy NFAT Beacon**          | lpha-nfat for NFAT lifecycle management                           |
| **Deploy Halo1**                | First Term Halo with legal buybox                          |
| **Onboard to Configurator**     | Create Halo1 inits, grant cBEAMs                                  |
| **Prime deployment**            | All Primes can deploy into Halo1 via spell-less Configurator flow |
| **Validate end-to-end**         | Confirm NFAT issuance, deployment, and redemption flow            |

---

### Phase 1.6: Term Halo Cohort

**Objective:** Complete initial Term Halo rollout

Deploy remaining first-cohort Term Halos:

| Halo | Description |
|------|-------------|
| **Halo2** | Term Halo partner  |
| **Halo3** | Term Halo partner |
| **Halo4** | Term Halo partner |
| **Halo5** | Term Halo partner |
| **Halo6** | Term Halo partner |

Each Term Halo follows the same pattern:
1. Deploy legal buybox
2. Onboard to Configurator (inits + cBEAMs)
3. Primes deploy immediately — no spell required

```
Execution Timeline:

1.0           1.1            1.2            1.3            1.4            1.5            1.6
Planning  →   Diamond    →   Operational →  Legacy      →  Configurator → Halo1       →  Full Cohort
              PAU Deploy     Infra          Cleanup        Deployment     (First Halo)   Deployment
                                            + Core Halos
    │             │              │              │              │              │              │
    ▼             ▼              ▼              ▼              ▼              ▼              ▼
┌────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐
│Timeline│  │Diamond   │  │Synome-MVP│  │Core Halos│  │Spell-less│  │NFAT      │  │Halo2     │
│agreed  │  │PAUs      │  │Verify    │  │ready     │  │operations│  │contracts │  │Halo3     │
│        │  │deployed  │  │Relay     │  │          │  │enabled   │  │Halo1 live│  │Halo4     │
│        │  │          │  │beacons   │  │          │  │          │  │          │  │Halo5     │
│        │  │          │  │          │  │          │  │          │  │          │  │Halo6     │
└────────┘  └──────────┘  └──────────┘  └──────────┘  └──────────┘  └──────────┘  └──────────┘
```

---

### End State

At completion of Phase 1.6:
- All first-cohort Primes operate without spells via Configurator
- Core Halos provide standardized access to legacy assets
- Six Term Halos (Halo1-Halo6) accepting NFAT deployments
- Verify beacon (lpla-verify) and executor beacons (lpha-relay, lpha-nfat) operational
- Synome-MVP tracking all risk parameters and NFAT records
