# Laniakea Phase 1: Proposed Spine Topics

**Status:** Draft for team review
**Prepared by:** Claude (Archon session)
**Last Updated:** 2026-02-23

---

## Methodology note

This document was produced by reading the full `laniakea-docs/` corpus and applying the spine-topic selection process described in the task brief. The Phase 1 scope document (`roadmap/phase-1-pragmatic-delivery.md`) was treated as the authoritative scoping filter. All candidate topics were subject to a cross-domain gate (≥2 distinct domains) and a hard-anchor gate (≥1 explicit table, schema, or open question in the repo).

**Phase 1 deliverables used as the scope filter** (from `roadmap/phase-1-pragmatic-delivery.md`):
1. Diamond PAU Deployment (substage 1.1)
2. Synome-MVP (substage 1.2)
3. MVP Beacons: lpla-verify, lpha-relay, lpha-nfat, lpha-report, lpha-council (substages 1.2–1.5)
4. Core Halos and Legacy Cleanup (substage 1.3)
5. Configurator Unit (substage 1.4)
6. NFAT Smart Contracts (substage 1.5)
7. Term Halo Legal (substage 1.5, legal prerequisite)

**Phase 1 note on settlement:** Phase 1 continues using the existing *manual* settlement process. lpla-verify monitors positions and calculates CRRs but does not track settlement — that capability arrives with lpla-checker in Phase 2.

---

## Glossary of terms used in this document

| Term | Plain-language definition |
|------|--------------------------|
| **Spine topic** | A cross-domain interface or constraint where ambiguity has high downstream cost and resolving it unlocks parallel execution |
| **PAU** | Parallelized Allocation Unit — the standard building block (Controller + ALMProxy + RateLimits) used at every layer |
| **CRR** | Capital Ratio Requirement — the capital a Prime must hold per dollar of a given position, based on the risk framework |
| **TRRC** | Total Required Risk Capital — the sum of CRR × position size across a Prime's full portfolio |
| **Synome-MVP** | The operational database for Phase 1, storing risk parameters, NFAT records, and position data |
| **aBEAM / cBEAM / pBEAM** | Admin / Configurator / Process BEAM — tiered on-chain authorized roles controlling PAU operations |
| **SORL** | Second-Order Rate Limit — a constraint that limits how fast a rate limit can be increased (default: 25% per 18h) |
| **NFAT** | Non-Fungible Allocation Token — an ERC-721 representing a bespoke deal claim between a Prime and a Halo |
| **Buybox** | The acceptable parameter ranges (duration, size, APY, asset types) for deals within an NFAT Facility |
| **Halo Sleeve** | The asset-side container backing one or more NFATs; the bankruptcy-remoteness boundary |
| **lpla-verify** | Low Power, Low Authority verify beacon — monitors positions, calculates CRRs, generates alerts (read-only) |
| **lpha-nfat** | Low Power, High Authority NFAT beacon — executes NFAT Facility lifecycle operations |
| **lpha-attest** | Low Power, High Authority attestation beacon — posts risk attestations about Halo Sleeve contents into Synome |
| **Attestor** | A company whitelisted by Sky governance to provide risk attestations; operates lpha-attest |
| **Init** | A pre-approved rate limit or controller action that GovOps can activate on a PAU via the Configurator |
| **EIP-2535** | Ethereum Improvement Proposal for the diamond proxy pattern — a modular, upgradeable contract architecture |
| **CoreHaloFacet** | A Diamond PAU facet that enables Prime deployment into legacy assets wrapped as Core Halos |

---

## A) Candidate Spine Topics Table

| Rank | Spine topic | Phase relevance | 1-sentence definition | Why it's a spine topic | Primary domains/functions | Evidence (repo anchors) | Key sub-decisions |
|------|-------------|-----------------|----------------------|------------------------|--------------------------|------------------------|------------------|
| 1 | **Synome-MVP: data schema, write authorization model, and API contract** | Phase 1 | The shared operational database for risk parameters, NFAT records, and position data — its schema, who can write what, and the interface beacons use to read/write it | Every Phase 1 beacon (lpla-verify, lpha-nfat, lpha-report, lpha-council) reads from or writes to Synome-MVP; if schema or write authorization is undefined, none can be built without conflicting assumptions | Data/infra (Archon), Risk (BA Labs), GovOps (Atlas Axis / OEA teams), Technical (PullUp) | `phase-1-pragmatic-delivery.md` §Synome-MVP (data flow table, "What Synome-MVP Stores" table); `governance-transition/council-beam-authority.md` (Synome write rights table); `smart-contracts/nfats.md` (onchain vs offchain data split table) | Schema fields per record type; signing/authentication mechanism for writes; API shape for beacon reads; versioning policy; how signed statements are validated |
| 2 | **Configurator Unit: aBEAM/cBEAM provision plan and init catalog for Phase 1** | Prerequisite for Phase 1 | The plan for which Core Council actions (inits, cBEAM grants) must be scheduled through the 14-day timelock, and which GovOps teams become accordant to which PAUs, in order to enable spell-less Prime operations by substage 1.4 | The 14-day BEAMTimeLock means aBEAM actions must be initiated 14 days before any GovOps team can operate; this must be coordinated across Core Council, multiple GovOps teams, and smart contract deployers well before substage 1.4 | Technical (PullUp/Dewiz/Sidestream), GovOps (Amatsu/Soter/Endgame Edge/Redline), Governance (Atlas Axis) | `smart-contracts/configurator-unit.md` (Role Capabilities table, Invariants list, Default Parameters table); `phase-1-pragmatic-delivery.md` §Configurator Unit (aBEAM/cBEAM flow diagram, SORL table); `governance-transition/council-beam-authority.md` (BEAM hierarchy table) | Which inits are pre-approved for Core Halos vs NFAT Facilities; SORL default values (hop, maxChange); 14-day timelock scheduling plan; cBEAM-to-GovOps-to-PAU mapping; pBEAM (relayer) grant protocol for LPHA beacons |
| 3 | **CRR calculation engine: lpla-verify data interface, formula mapping, and alert output** | Phase 1 | The specification of what on-chain and Synome data lpla-verify ingests, which risk-framework formula applies to each asset type (Core Halo, NFAT sleeve phase), and what format its outputs (CRR alerts, TRRC, Encumbrance Ratio) take | lpla-verify must be operational at substage 1.2 to unblock Core Halo deployment monitoring; the risk framework specifies formulas, but lpla-verify's exact data inputs from Synome-MVP and output contract are not specified in the corpus — BA Labs must provide this for Archon to build | Risk (BA Labs), Data/infra (Archon), GovOps (Atlas Axis and all OEA teams as consumers) | `risk-framework/sentinel-integration.md` (CRR/TRRC/TRC/Encumbrance Ratio definition table); `risk-framework/capital-formula.md` (per-position formulas); `smart-contracts/nfats.md` (sleeve-phase CRR table); `risk-framework/README.md` (open item: "Beacon implementation — Formulas and algorithms for lpla-checker calculations") | Exact input fields pulled from Synome vs on-chain; price feed sources and update frequency; CRR formula per sleeve phase (Filling/Deploying/At Rest/Missed re-attestation); TRRC and Encumbrance Ratio output format; alert threshold definitions and escalation path |
| 4 | **NFAT Facility onboarding mechanism and Configurator integration** | Phase 1 | How a Prime gets approved to deposit into a specific NFAT Facility — the governance and Configurator integration path from Prime synomic approval through to queue deposit — which is explicitly marked as an open question in the NFATS spec | This open question blocks all Term Halo deployment (substage 1.5); Archon cannot build lpha-nfat's pBEAM logic, GovOps cannot operate, and PullUp/Dewiz/Sidestream cannot finalize contracts without knowing the approval flow | Technical (PullUp/Dewiz/Sidestream), Data/infra (Archon), GovOps (Amatsu/Soter/Endgame Edge/Redline), Governance (Atlas Axis) | `smart-contracts/nfats.md` L204–206 (explicit open question: "The exact onboarding mechanism for Primes to Facilities is an open design question"); `phase-1-pragmatic-delivery.md` §NFAT Smart Contracts; `smart-contracts/configurator-unit.md` (cBEAM/init model) | Whether Facility onboarding uses Configurator inits or a separate governance spell; who triggers onboarding and via which contract; what constitutes Prime synomic governance approval; how lpha-nfat receives pBEAM for a new Facility |
| 5 | **Term Halo legal infrastructure: buybox template, entity structure, and recourse mechanics** | Phase 1 | The legal framework that must be in place before lpha-nfat can execute deals autonomously — buybox templates defining acceptable deal parameters, pre-signed master agreements for Prime participation, and Fortification Conserver intervention procedures | Without the legal infrastructure, lpha-nfat cannot execute deals without per-deal governance approval, blocking the autonomous operation model for all 6 Term Halos in substages 1.5–1.6; Frontier Foundation and OEA GovOps are likely to hold different assumptions about the legal entity structure | Legal (Frontier Foundation), GovOps (Redline/Endgame Edge as Operational Facilitators), Risk (BA Labs for CRR treatment of assets within buybox), Technical (PullUp/Dewiz/Sidestream for smart contract parameters) | `phase-1-pragmatic-delivery.md` §Term Halo Legal (Design Principles table, Buybox table, Deliverables list); `sky-agents/halo-agents/term-halo.md` (Governance Artifacts table, Bankruptcy Remoteness section); `smart-contracts/nfats.md` (Terms Source table, Ecosystem accord mode) | Legal entity structure for Halo Sleeves (serialized LLC equivalent — jurisdiction and structure TBD); which parameters constitute the buybox for Halo1; form of pre-signed Prime master agreement; Fortification Conserver trigger conditions and intervention procedure; whether Unit Artifact is a legal document or a Synome record |
| 6 | **Core Halo classification decisions, CoreHaloFacet interface, and oracle data requirements** | Phase 1 | The decisions about which legacy assets are promoted to Core Halos versus wound down, the standardized CoreHaloFacet function set that wraps them, and the oracle/position data each Core Halo must expose for lpla-verify and lpha-collateral | Phase 1.3 (legacy cleanup) is a prerequisite for 1.4 (Configurator deployment with Core Halo allocation targets); inconsistent or missing oracle data breaks CRR calculations for all wrapped legacy positions; different GovOps teams may hold different views on which assets are "worth retaining" | GovOps (Endgame Edge/Redline/Amatsu/Soter as operators of respective Primes), Technical (PullUp/Dewiz/Sidestream for CoreHaloFacet), Risk (BA Labs for asset classification and CRR), Data/infra (Archon for lpla-verify data ingestion) | `phase-1-pragmatic-delivery.md` §Core Halos (Decision Tree diagram, Cleanup Targets table, Acceptance Criteria); `smart-contracts/architecture-overview.md` (Prime → Core Halos connection table; "CoreHaloFacet — Deploy into Core Halo positions"); `risk-framework/asset-type-treatment.md` (asset classification framework) | Which specific assets (Morpho vaults, SparkLend, Aave pools, Spark, Phase 0 Grove assets) are promoted vs wound down; CoreHaloFacet function set covering DeFi-specific interfaces (Aave, Morpho, Curve); oracle data fields required per Core Halo type for CRR calculation; Halo Unit Artifact template fields |
| 7 | **Attestor governance: selection, whitelisting, and lpha-attest Synome interface** | Phase 1 | The governance process for selecting and whitelisting the Attestor company that will operate lpha-attest, and the attestation schema (fields, format, cadence) they must write into Synome to trigger CRR phase transitions for NFAT sleeves | Without a whitelisted Attestor and an agreed attestation schema, no NFAT sleeve can transition from Filling to Deploying (the two-beacon gate blocks all capital deployment); BA Labs and Archon need the schema to tie CRR phase transitions to attestation events | Governance (Frontier Foundation for Attestor selection and whitelisting; Atlas Axis/Core Council for governance approval), Data/infra (Archon builds lpha-attest), Risk (BA Labs specifies CRR per attestation state), GovOps (OEA teams as NFAT operators) | `smart-contracts/nfats.md` (The Attestor and lpha-attest section; two-beacon deployment gate diagram; attestation types table; CRR Incentive Structure table); `sky-agents/halo-agents/term-halo.md` (lpha-attest beacon section); `risk-framework/capital-formula.md` (Term Halo NFAT CRR reference) | Attestor selection criteria and governance whitelisting process; attestation schema (required fields for pre-deployment and post-deployment attestations); re-attestation cadence per asset type; what data lpha-attest writes to Synome; how lpla-verify reads attestation state to determine which CRR formula to apply |
| 8 | **Diamond PAU architecture: Phase 1 facet set, audit requirements, and migration protocol** | Prerequisite for Phase 1 | The specific set of EIP-2535 facets included in Phase 1 Diamond PAUs, the testing and audit criteria before migration, and the transaction sequence (deploy → migrate → grant cBEAMs → wind down legacy) across the first-cohort Primes | All Phase 1 deliverables depend on Diamond PAUs being deployed and operational at substage 1.1; PullUp/Dewiz/Sidestream must agree on the facet set, GovOps needs the migration procedure, and Core Council must time the cBEAM re-grants through the 14-day timelock | Technical (PullUp/Dewiz/Sidestream for contract development and deployment), GovOps (Amatsu/Soter/Endgame Edge/Redline for migration execution), Governance (Atlas Axis/Core Council for cBEAM re-grants) | `phase-1-pragmatic-delivery.md` §Diamond PAU Deployment (Architecture table, Phase 1 Action Facets table, Migration Path 4-step sequence, substage 1.1 task table); `smart-contracts/diamond-pau.md`; `smart-contracts/architecture-overview.md` (Legacy PAU vs Diamond PAU comparison table) | Which Primes are in the first cohort for 1.1 vs deferred to later substages; complete facet list for Phase 1 Diamond PAU including LegacyMigrationFacet scope; audit requirements (separate audit or reuse of existing ALM controller audits?); migration transaction sequence and rollback procedure; cBEAM re-grant scheduling relative to migration |
| 9 | **Phase 1 manual settlement continuity: lpla-verify output contract and inter-team handoff** | Phase 1 | The specification of what lpla-verify outputs (CRR reports, position verifications, alerts) are usable by the manual settlement process, and which teams consume which outputs — since lpla-verify cannot track settlement itself in Phase 1 | Phase 1 has multiple GovOps teams (Atlas Axis, Amatsu, Soter, Endgame Edge, Redline) and BA Labs all potentially involved in the monthly manual settlement; the Phase 1 doc explicitly notes that lpla-verify does not track settlement, yet does not specify how its outputs feed the settlement calculation — each team may assume a different handoff | Risk (BA Labs — settlement methodology), GovOps (Atlas Axis as Core Council GovOps; Amatsu/Soter/Endgame Edge/Redline as OEA GovOps), Data/infra (Archon — lpla-verify implementation) | `phase-1-pragmatic-delivery.md` §Executive Summary ("Phase 1 continues using the existing manual settlement process"); `accounting/prime-settlement-methodology.md` (five-step calculation; data inputs per step); `roadmap/roadmap-overview.md` (Phase 1 "lpla-verify: monitors positions, calculates CRRs, generates alerts. Does not track settlement"); `risk-framework/sentinel-integration.md` (lpla-checker metrics table) | What lpla-verify writes vs doesn't write relative to settlement; who reads lpla-verify outputs for the five-step settlement calculation; settlement execution responsibility across teams; data format handoff between Synome-MVP outputs and BA Labs settlement calculation; when in the month lpla-verify outputs are considered authoritative |

---

## B) Detail Sections

---

### 1. Synome-MVP: Data Schema, Write Authorization Model, and API Contract

#### 1.1 Definition (plain language)

Synome-MVP is the Phase 1 operational database. It is the source of truth for risk parameters, NFAT deal records, and position data. Every beacon in Phase 1 either reads from it (lpla-verify, lpha-nfat) or writes to it (lpha-report, lpha-council, lpha-nfat). "Schema" here means the structure of each record type — what fields it contains, what types they are, and what they mean. "Write authorization" means which signed statements from which actors are accepted as writes.

#### 1.2 Why it unblocks execution

- lpla-verify cannot calculate CRRs without knowing the exact field names and types for risk parameters in Synome-MVP.
- lpha-nfat cannot write NFAT records without knowing the required fields (APY, duration, maturity, sleeve assignment, etc.).
- lpha-council cannot push risk equations without knowing the schema it is updating.
- lpha-report cannot post performance summaries without knowing the format.
- Downstream tools (manual settlement, BA Labs dashboards) cannot consume Synome-MVP outputs without a stable schema.

If schema is agreed, all five beacons can be built and tested in parallel. Without it, each team builds against its own guess and integration fails.

#### 1.3 Known ambiguities / mismatch risk

- **Who writes risk parameters:** The Phase 1 doc says "Core Council GovOps" writes signed risk parameter updates, but "Core Council GovOps" maps to Atlas Axis as a team. It's unclear whether Atlas Axis drafts the parameters and lpha-council submits them, or Atlas Axis uses lpha-council as a passthrough, or something else.
- **NFAT record ownership:** lpha-nfat writes NFAT records to Synome-MVP, but lpha-attest also writes attestations. If both write to the same record, there's a write-conflict risk unless the schema separates them.
- **Authentication model:** The Phase 1 doc says "signed statements from authorized parties" but doesn't specify the signing scheme (on-chain via BEAM? off-chain key?). Archon may assume a different authentication model than the governance documents intend.
- **Schema versioning:** The Phase 1 doc says Synome-MVP stores risk parameters "signed by Core Council GovOps" but doesn't specify how schema changes are versioned or how backward compatibility is maintained across Phase 1 → Phase 2.

#### 1.4 What would settle it (v0)

A v0 Synome-MVP schema spec that covers:
1. Record types and required fields for each (risk parameters, NFAT records, position data, performance summaries, attestations).
2. Write authorization model: which role/key/BEAM can write which record type.
3. Read API: the interface beacons and manual tools use to query Synome-MVP.
4. Validation rules: what makes a write rejected vs accepted.

#### 1.5 Proposed deliverables

- **Synome-MVP Schema v0** — field definitions, types, validation rules for each record type
- **Write Authorization Table** — maps record type to authorized writer (role/key/BEAM)
- **Read API Spec** — query interface for beacon ingestion and manual tooling
- **Schema Versioning Policy** — how schema changes will be managed through Phase 1 without breaking running beacons

#### 1.6 Evidence

| Source | Anchor |
|--------|--------|
| `roadmap/phase-1-pragmatic-delivery.md` §Synome-MVP | Data flow table (Source/Data/Consumer); "What Synome-MVP Stores" table (3 record types) |
| `governance-transition/council-beam-authority.md` | Synome write rights table (6 write right types, grantors) |
| `smart-contracts/nfats.md` | Onchain vs offchain data split table (Deal NFAT onchain fields vs Synome fields) |
| `roadmap/phase-1-pragmatic-delivery.md` §Council Beacon | "Enable Core Council to update risk equations and specify report formats" |

---

### 2. Configurator Unit: aBEAM/cBEAM Provision Plan and Init Catalog for Phase 1

#### 2.1 Definition (plain language)

The Configurator Unit is the governance layer that lets GovOps teams manage Prime operations without full governance spells. For it to work, the Core Council must have pre-approved a set of "inits" (allowed rate limit values and controller actions) via a 14-day timelock, and must have granted "cBEAMs" (operational roles) to the right GovOps teams for the right PAUs — also via the 14-day timelock. This spine topic is about what the init catalog should contain, which GovOps teams get which cBEAMs for which PAUs, and the scheduling plan so the 14-day timelock doesn't become a Phase 1 blocker.

#### 2.2 Why it unblocks execution

Phase 1.4 (substage "Configurator Deployment") requires:
- All Core Halo allocation targets pre-registered with inits
- cBEAMs granted to respective GovOps teams

Since every aBEAM action has a 14-day timelock, these actions must be initiated at least 14 days before substage 1.4. If the init catalog isn't agreed in advance, Core Council cannot initiate the timelock on time. If GovOps teams don't know which PAUs they'll be accordant to, they can't prepare their relayer infrastructure.

#### 2.3 Known ambiguities / mismatch risk

- **Init values:** The Configurator spec lists default parameters (hop=18h, maxChange=25%) but doesn't specify what initial maxAmount or slope values are appropriate for Core Halos. BA Labs needs to inform these values; Core Council needs to approve them; GovOps needs to operate within them. Three different teams likely have different expectations.
- **cBEAM assignments:** It's not clear which OEA GovOps team (Amatsu, Soter, Endgame Edge, or Redline) will receive cBEAMs for which specific Prime PAUs. The team-to-Prime mapping isn't specified in the corpus.
- **NFAT Facility inits:** The Phase 1 doc says GovOps "creates Halo1 inits, grants cBEAMs" at substage 1.5, but the Configurator spec distinguishes between global inits (any PAU) and restricted inits (specific PAU). Which type is appropriate for Facilities is unspecified.
- **pBEAM grants for LPHA beacons:** How lpha-relay and lpha-nfat receive pBEAMs (relayer role) on their respective PAUs is not spelled out. The Configurator spec says GovOps "sets relayer address via setRelayer" — but who gives pBEAMs to the beacon programs, and under what oversight?

#### 2.4 What would settle it (v0)

1. Init catalog v0: list of all inits required for Phase 1 Core Halos and NFAT Facilities, with initial maxAmount and slope values (requires BA Labs input).
2. cBEAM assignment map: which GovOps team is accordant to which PAUs (requires team coordination).
3. Timelock schedule: aBEAM action schedule aligned with Phase 1 substage dates.
4. pBEAM grant procedure: how LPHA beacons receive relayer roles on PAUs.

#### 2.5 Proposed deliverables

- **Phase 1 Init Catalog** — all required inits with values, for Core Halos and NFAT Facilities
- **cBEAM Assignment Map** — PAU-to-GovOps-team mapping
- **aBEAM Scheduling Plan** — timelock actions with target dates relative to substage milestones
- **pBEAM Grant Procedure** — how LPHA beacons receive and maintain relayer authority

#### 2.6 Evidence

| Source | Anchor |
|--------|--------|
| `smart-contracts/configurator-unit.md` | Role Capabilities table (aBEAM and cBEAM actions); Invariants list (7 explicit invariants); Default Parameters table (hop=18h, maxChange=25%, timelock=14 days) |
| `phase-1-pragmatic-delivery.md` §Configurator Unit | aBEAM/cBEAM flow diagram; "typical flow" 5-step description; SORL safety property table |
| `governance-transition/council-beam-authority.md` | BEAM hierarchy table (Council Beacon → aBEAM → cBEAM → pBEAM → PAU) |
| `phase-1-pragmatic-delivery.md` §Phase 1.4 | Task table for substage 1.4 ("Register Core Halos", "Grant cBEAMs") |

---

### 3. CRR Calculation Engine: lpla-verify Data Interface, Formula Mapping, and Alert Output

#### 3.1 Definition (plain language)

lpla-verify is the Phase 1 monitoring beacon. To run, it needs to know exactly what data to pull from Synome-MVP and on-chain, which risk-framework formula to apply per position type, and what to output (CRR per position, TRRC, Encumbrance Ratio, alerts). The risk framework specifies the formulas in detail (in `risk-framework/`), but the interface between the framework and the beacon implementation is an open item explicitly flagged in `risk-framework/README.md`.

#### 3.2 Why it unblocks execution

lpla-verify must be operational at substage 1.2 to enable Core Halo monitoring. BA Labs owns the risk framework formulas; Archon builds the beacon. Without a v0 interface spec:
- Archon cannot start building lpla-verify without making assumptions about data inputs.
- BA Labs cannot validate correctness without knowing what lpla-verify will compute.
- GovOps teams cannot act on alerts whose format is undefined.

This is a classic two-team interface gap: the formula owners and the implementation owners haven't specified the handoff contract.

#### 3.3 Known ambiguities / mismatch risk

- **NFAT sleeve phase data:** lpla-verify must know the current sleeve phase (Filling/Deploying/At Rest/Missed re-attestation) to apply the right CRR. This data comes from lpha-attest writing attestations to Synome-MVP. If the attestation schema isn't agreed (see spine topic 7), lpla-verify can't determine sleeve phase.
- **Price feed sources:** The capital formula requires "dollars first" for position sizes. Which oracle feeds provide prices for Core Halo assets (Morpho, SparkLend, etc.) is unspecified.
- **Alert format and recipients:** Who receives CRR alerts from lpla-verify, in what format, via what channel, is not specified. GovOps teams may have different expectations about alert immediacy.
- **SOFR validation:** lpla-verify must "validate that Primes deploying into duration NFATs have declared either hedge positions or SOFR plus terms" (Phase 1 doc §SOFR Hedging). The format of this declaration is unspecified.

#### 3.4 What would settle it (v0)

A v0 lpla-verify specification covering:
1. Input data sources (Synome-MVP fields + on-chain sources + price feeds).
2. CRR formula mapping per asset type and sleeve phase.
3. Output format: fields for CRR per position, TRRC, TRC, Encumbrance Ratio, and SOFR validation status.
4. Alert definitions, thresholds, and routing.

#### 3.5 Proposed deliverables

- **lpla-verify Data Interface Spec** — input fields, sources, and refresh cadence
- **CRR Formula Mapping Table** — formula per asset type and condition (Core Halo type, NFAT sleeve phase, SOFR validation status)
- **Alert Specification** — threshold definitions, output format, recipient routing
- **lpla-verify Acceptance Tests** — test cases BA Labs can use to validate implementation correctness

#### 3.6 Evidence

| Source | Anchor |
|--------|--------|
| `risk-framework/README.md` | Open item: "Beacon implementation — Formulas and algorithms for lpla-checker calculations" |
| `risk-framework/sentinel-integration.md` | Metrics table: CRR, TRRC, TRC, Encumbrance Ratio definitions; lpla-checker use case |
| `risk-framework/capital-formula.md` | Per-position CRR formulas; Duration Capacity calculation |
| `smart-contracts/nfats.md` | Sleeve-phase CRR table (Filling=low, Deploying=high, At Rest=medium, Missed re-attestation=increases) |
| `phase-1-pragmatic-delivery.md` §SOFR Hedging | "lpla-verify validates that Primes deploying into duration NFATs have declared either hedge positions or SOFR plus terms" |

---

### 4. NFAT Facility Onboarding Mechanism and Configurator Integration

#### 4.1 Definition (plain language)

For a Prime to queue capital into an NFAT Facility, something must approve that Prime for that Facility. The NFATS spec describes the flow conceptually ("Prime synomic governance approves the Facility → Prime can deposit into queue → Halo can claim") but explicitly marks the "specific governance and Configurator integration path" as an open design question. This spine topic is about resolving that question: what is the onboarding mechanism, and how does it connect to the Configurator Unit's init/cBEAM model?

#### 4.2 Why it unblocks execution

The open design question directly blocks substage 1.5 (First Term Halo). Archon cannot finalize lpha-nfat's pBEAM grant logic without knowing the approval flow. PullUp/Dewiz/Sidestream cannot finalize the NFAT Facility contracts without knowing whether they need to read from the Configurator. GovOps teams (who will operate Facilities) cannot define their procedures.

The question also has a 14-day timelock dependency: if Facility onboarding goes through BEAMTimeLock, the approval must be initiated 14 days before a Prime can deposit. This creates a hard scheduling constraint relative to substage 1.5.

#### 4.3 Known ambiguities / mismatch risk

- **Configurator vs separate spell:** Some designs imply Facility onboarding uses the same Configurator init system as Core Halos (same infrastructure, different target). Others imply it uses a separate governance spell specific to NFAT Facilities. These are architecturally different.
- **Governance approval definition:** "Prime synomic governance approves the Facility" is vague. This could mean the Prime's GovOps team approves (an operational decision), the Core Council approves (a governance spell), or a combination.
- **Rate limit at the Facility level:** The Phase 1 doc mentions GovOps "creates Halo1 inits, grants cBEAMs" but it's unclear whether these inits are per-Prime or per-Facility.
- **Interaction with lpha-nfat pBEAM:** lpha-nfat holds pBEAM for Facility operations. How the pBEAM connects to Prime-level approval is unspecified.

#### 4.4 What would settle it (v0)

A v0 Facility onboarding spec:
1. The governance/approval mechanism (Configurator init? Spell? Synome record?).
2. The sequence diagram from "Prime wants to deploy into Facility X" to "Prime can deposit into queue."
3. How lpha-nfat's pBEAM is granted and scoped per Facility.
4. Whether/how timelock applies to each step.

#### 4.5 Proposed deliverables

- **NFAT Facility Onboarding Spec** — sequence diagram, governance steps, contract interactions
- **Configurator Integration Decision Record** — whether Facility onboarding uses init/cBEAM model or a separate mechanism
- **lpha-nfat pBEAM Grant Procedure** — how Archon-operated beacon receives Facility-level execution authority

#### 4.6 Evidence

| Source | Anchor |
|--------|--------|
| `smart-contracts/nfats.md` L204–206 | Explicit open question: "The exact onboarding mechanism for Primes to Facilities is an open design question. The flow is: Prime synomic governance approves the Facility → Prime can deposit into queue → Halo can claim. The specific governance and Configurator integration path needs further specification." |
| `phase-1-pragmatic-delivery.md` §Phase 1.5 | "Onboard to Configurator: Create Halo1 inits, grant cBEAMs" |
| `smart-contracts/nfats.md` §Deal Lifecycle | "Prime synomic governance approves deployment into NFAT Facility / Govops onboards Facility via configurator (rate limits) or timelock (BEAMstate)" — dual path described but not specified |

---

### 5. Term Halo Legal Infrastructure: Buybox Template, Entity Structure, and Recourse Mechanics

#### 5.1 Definition (plain language)

lpha-nfat executes deals autonomously — it can claim from queues and mint NFATs — but only within a pre-agreed legal framework. That framework consists of: (a) the buybox template defining acceptable parameter ranges, (b) a pre-signed master agreement that Primes sign to participate in Facilities, (c) the legal entity structure for Halo Sleeves (described as "serialized LLC equivalent"), and (d) the intervention procedures for the Fortification Conserver if a Halo fails to fund at maturity. All four are listed as Phase 1 deliverables but none are specified in the corpus.

#### 5.2 Why it unblocks execution

Without the legal infrastructure:
- lpha-nfat cannot operate autonomously — every deal would require a separate governance approval.
- No Prime can safely queue capital without a signed agreement governing its recourse rights.
- Halo partners (Halo1–Halo6) cannot onboard without knowing the entity structure and buybox boundaries.
- The Halo Sleeve's bankruptcy remoteness claim requires the correct legal structure to be credible.

Legal lead time for entity formation and agreement templates is typically 4–8 weeks (as noted in `term-halo.md`). Given that substage 1.5 requires Halo1 live, legal work must start early in the Phase 1 timeline.

#### 5.3 Known ambiguities / mismatch risk

- **Entity jurisdiction:** The Phase 1 doc calls Halo Sleeves "serialized LLC equivalents" but doesn't specify jurisdiction. Delaware LLCs, Cayman SPVs, and UK LLPs each have different formation requirements and recourse mechanics.
- **Buybox parameter ownership:** The Phase 1 doc lists example ranges but notes these are "example ranges." Who defines the final buybox for each Halo (Frontier Foundation? OEA GovOps? the asset manager?)
- **Pre-signature mechanics:** "Primes and counterparties pre-sign agreements covering the full buybox parameter range" — it's unclear whether this is a single master agreement or per-Facility, and what happens if a Prime hasn't signed when lpha-nfat tries to execute a deal.
- **Fortification Conserver:** The term appears in the Phase 1 doc and term-halo.md but is not defined. Who or what is the Fortification Conserver? A legal entity? A multisig? A role held by Core Council?

#### 5.4 What would settle it (v0)

1. Legal entity structure decision: jurisdiction, formation mechanics, and how Sleeves are created/closed.
2. Buybox template v0 for Halo1: specific parameter ranges and asset type definitions.
3. Prime master agreement template: the form of pre-signed agreement.
4. Fortification Conserver definition and intervention trigger.

#### 5.5 Proposed deliverables

- **Term Halo Legal Framework v0** — entity structure decision, jurisdiction, formation mechanics
- **Buybox Template v0** — parameter ranges for Halo1 (to be adapted per Halo)
- **Prime Master Agreement Template** — form document for Prime participation
- **Recourse and Intervention Procedures** — Fortification Conserver role, trigger conditions, and intervention process
- **Artifact Templates** — Halo Artifact and Unit Artifact document templates (as listed in Phase 1 deliverables)

#### 5.6 Evidence

| Source | Anchor |
|--------|--------|
| `phase-1-pragmatic-delivery.md` §Term Halo Legal | Buybox parameter table (Duration 6-24mo, Size 5M-100M, APY 8-15%, etc.); Deliverables list (4 items); "serialized LLC equivalent" quote |
| `sky-agents/halo-agents/term-halo.md` §Legal Infrastructure | Governance Artifacts table (Halo Artifact, Unit Artifact, Sleeve Records, Synome Records) |
| `smart-contracts/nfats.md` §Design Rationale | "Pre-signed Integration — Primes and counterparties pre-sign agreements covering the full buybox parameter range" |
| `phase-1-pragmatic-delivery.md` §Design Principles | "Fortification Conserver can assume control of NFAT Facility assets if legal intervention is needed" |

---

### 6. Core Halo Classification Decisions, CoreHaloFacet Interface, and Oracle Data Requirements

#### 6.1 Definition (plain language)

Before the Configurator can register Core Halo allocation targets (substage 1.4), three things must be decided: (1) which specific legacy assets are promoted to Core Halos versus wound down; (2) what the CoreHaloFacet contract exposes — the function set that enables Prime deployment into each type of legacy DeFi position (Morpho, SparkLend, Aave, etc.); and (3) what oracle and position data each Core Halo must expose for lpla-verify (CRR calculation) and lpha-collateral (position reporting).

#### 6.2 Why it unblocks execution

Phase 1.3 (Legacy Cleanup and Core Halos) is a prerequisite for 1.4. If the classification decisions aren't made:
- GovOps teams can't wind down the right positions.
- PullUp/Dewiz/Sidestream can't build the CoreHaloFacet for the correct target set.
- The Configurator's init catalog (spine topic 2) can't be finalized because it depends on knowing which Core Halos exist.
- BA Labs can't classify Core Halo assets for CRR purposes without knowing what they are.

Different GovOps teams likely have different views on "worth retaining" — positions that are profitable for one Prime may be redundant or risky viewed across all Primes.

#### 6.3 Known ambiguities / mismatch risk

- **Phase 0 Grove assets:** Phase 0 explicitly creates several legacy positions (custodial crypto lending, crypto-enabled lending, tokenized RWA trading, bridge) as acknowledged technical debt. Which of these become Core Halos, and how they're classified for CRR purposes, is not specified.
- **Spark position:** Spark is a Genesis Star operated by Endgame Edge. If Spark allocations are Core Halos controlled by Core Council, there's a potential conflict with Spark's own governance. This tension is implicit but not addressed.
- **CoreHaloFacet function set:** The Phase 1 doc lists `CoreHaloFacet` as a facet but doesn't specify its functions beyond "Deploy into Core Halo positions." Morpho, SparkLend, Aave, Curve, and Uniswap all have different interfaces.
- **Oracle data requirements:** lpla-verify needs position data and price data for Core Halos. For DeFi positions, this may come from on-chain reads; for legacy RWA positions (Phase 0 Grove assets), this is unclear.

#### 6.4 What would settle it (v0)

1. Core Halo registry: list of all assets in/out, with rationale.
2. Asset classification for each Core Halo (for CRR purposes).
3. CoreHaloFacet function list covering all required DeFi interface types.
4. Oracle/position data specification per Core Halo type.

#### 6.5 Proposed deliverables

- **Core Halo Classification Register** — enumeration of all assets with in/out decision and rationale
- **Core Halo Asset Classification** — CRR category for each (for BA Labs risk framework)
- **CoreHaloFacet Interface Spec** — function list and parameter types for each DeFi target type
- **Core Halo Oracle Data Spec** — required oracle/position data fields per Core Halo type (for lpla-verify and lpha-collateral)
- **Halo Unit Artifact Template** — governance documentation fields for Core Halos

#### 6.6 Evidence

| Source | Anchor |
|--------|--------|
| `phase-1-pragmatic-delivery.md` §Core Halos | Decision Tree diagram; "Initial scope" description (Morpho vaults, SparkLend, other DeFi integrations); Acceptance Criteria (three bullet points) |
| `smart-contracts/architecture-overview.md` | "Core Halos — legacy DeFi (Morpho vaults, Aave pools, SparkLend) wrapped as Halo Units under Core Council governance"; Prime→Core Halos connection type (CoreHaloFacet) |
| `roadmap/phase-0-legacy-exceptions.md` | Phase 0 asset inventory (custodial crypto lending, crypto-enabled lending, tokenized RWA, bridge, Star4) — all become Core Halos in 1.3 |
| `risk-framework/asset-type-treatment.md` | Asset type treatment framework (relevant to Core Halo classification for CRR) |

---

### 7. Attestor Governance: Selection, Whitelisting, and lpha-attest Synome Interface

#### 7.1 Definition (plain language)

The Attestor is a company whitelisted by Sky governance that provides risk attestations about NFAT Halo Sleeve contents. It operates the lpha-attest beacon. Without an Attestor, no NFAT sleeve can transition from Filling (capital queued) to Deploying (capital deployed) — the "two-beacon deployment gate" requires an lpha-attest attestation before lpha-nfat can change sleeve status. This spine topic covers: who selects the Attestor and how they are whitelisted, what attestation schema lpha-attest must write into Synome, and how BA Labs's CRR phase-transition logic maps to attestation events.

#### 7.2 Why it unblocks execution

The two-beacon gate is a hard dependency for all Term Halo capital deployment. Without an Attestor:
- No sleeve can transition from Filling to Deploying.
- No capital reaches RWA endpoints.
- All Term Halos are operationally blocked even if smart contracts are deployed and legal framework is complete.

Additionally, the Attestor must be established and operating *before* Halo1 goes live. Governance whitelisting (likely via a spell or aBEAM action) has its own lead time.

#### 7.3 Known ambiguities / mismatch risk

- **Attestor identity:** The corpus says "a company whitelisted by Sky governance" but doesn't name a candidate or describe selection criteria. Frontier Foundation likely has a view; Core Council may have different requirements; BA Labs needs the attestation format to match the CRR model.
- **Attestation schema:** The NFATS spec describes attestation *content* ("assets will deploy into [risk characteristics]...") but not the Synome schema (field names, types, required vs optional). BA Labs and Archon need this agreed before they can connect CRR phase transitions to attestation events.
- **Re-attestation cadence:** The NFATS spec says "cadence varies by asset and directly affects CRR — more frequent attestation enables lower CRR." Who decides the cadence per asset type? BA Labs (risk calibration) or the Attestor (operational feasibility)?
- **Privacy vs CRR tradeoff:** The Attestor provides *risk characteristics* about a sleeve, not individual borrower details. BA Labs needs to calibrate CRR based on these risk characteristics — but the attributes they can observe are constrained by the privacy model. This tradeoff needs explicit agreement.

#### 7.4 What would settle it (v0)

1. Attestor selection process and governance whitelisting procedure.
2. Attestation schema v0: required fields, types, and semantics for pre-deployment, post-deployment, and re-attestation events.
3. Re-attestation cadence table: cadence per asset type and resulting CRR impact.
4. Mapping from attestation state to CRR formula (for BA Labs and Archon alignment).

#### 7.5 Proposed deliverables

- **Attestor Selection and Governance Procedure** — selection criteria, whitelisting mechanism, accountability model
- **lpha-attest Attestation Schema v0** — Synome record fields for each attestation type
- **Re-attestation Cadence Table** — per asset type, with CRR impact
- **Attestation-to-CRR Mapping** — how each attestation state maps to lpla-verify's CRR formula selection

#### 7.6 Evidence

| Source | Anchor |
|--------|--------|
| `smart-contracts/nfats.md` §The Attestor and lpha-attest | Attestor properties table; two-beacon deployment gate diagram; attestation types table (3 types with content) |
| `smart-contracts/nfats.md` §CRR Incentive Structure | CRR impact table per sleeve phase (4 phases with CRR impact and incentive) |
| `sky-agents/halo-agents/term-halo.md` §lpha-attest | "Attestor company whitelisted by Sky governance"; two-beacon gate sequence |
| `risk-framework/capital-formula.md` | "For positions held via Term Halo sleeves, CRR varies by sleeve phase" |

---

### 8. Diamond PAU Architecture: Phase 1 Facet Set, Audit Requirements, and Migration Protocol

#### 8.1 Definition (plain language)

Diamond PAU (EIP-2535) replaces the legacy PAU architecture at substage 1.1. The diamond proxy pattern allows new functionality to be added as modular "facets" without redeploying the entire contract. For Phase 1, this means specifying exactly which facets are included in the initial deployment, what testing and audit coverage is required before migration, and the step-by-step sequence for migrating each Prime from legacy PAU to Diamond PAU. The migration requires re-granting cBEAMs through the 14-day timelock — creating a hard dependency on substage 1.1 completing before the Configurator timelock can begin.

#### 8.2 Why it unblocks execution

Diamond PAU deployment (substage 1.1) is the first operational substage after planning. All subsequent infrastructure (Synome-MVP, Configurator, Beacons, Term Halos) assumes Diamond PAUs are in place. Additionally, the 14-day timelock for cBEAM grants after migration (spine topic 2) means the migration timeline sets the earliest possible date for substage 1.4. If migration slips, all downstream substages slip.

#### 8.3 Known ambiguities / mismatch risk

- **Facet set completeness:** The Phase 1 doc lists `NfatDepositFacet`, `NfatWithdrawFacet`, `CoreHaloFacet`, and `LegacyMigrationFacet`. It's unclear whether additional facets (e.g., for bridge operations, for SOFR hedging validation, for future compatibility) should be included. Including too few requires future facet additions through governance; including too many expands the audit surface.
- **Audit strategy:** The architecture overview notes "same contracts, different configuration" and that factories can stamp out PAUs — implying audit reuse is a key benefit. Whether Phase 1 Diamond PAUs can rely on existing ALM controller audits or require a full new audit is not specified. This materially affects timeline.
- **Migration sequencing:** Which Primes are in the "first cohort" for substage 1.1 is not specified in the Phase 1 doc. If multiple Primes need to be migrated simultaneously, operational complexity increases.
- **Legacy PAU wind-down timing:** The Phase 1 doc says "Wind down legacy PAU once migration complete." But if legacy positions are still active (Phase 0 Grove assets pending Core Halo wrapping), the wind-down timing becomes ambiguous.

#### 8.4 What would settle it (v0)

1. Phase 1 facet list: definitive list of facets for initial Diamond PAU deployment.
2. Audit strategy: new audit vs incremental audit vs existing coverage, with acceptance criteria.
3. First cohort Prime list: which Primes migrate at substage 1.1.
4. Migration procedure: step-by-step sequence including cBEAM re-grant timing and legacy PAU wind-down criteria.

#### 8.5 Proposed deliverables

- **Phase 1 Diamond PAU Facet Specification** — complete facet list with function signatures and interfaces
- **Audit Plan** — audit scope, coverage requirements, acceptance criteria
- **First Cohort Migration Plan** — Prime list, migration sequence, rollback procedure
- **cBEAM Re-grant Schedule** — timing relative to substage 1.1 completion and substage 1.4 requirements

#### 8.6 Evidence

| Source | Anchor |
|--------|--------|
| `phase-1-pragmatic-delivery.md` §Diamond PAU Deployment | Architecture table; Phase 1 Action Facets table (4 facets); Migration Path 4-step sequence |
| `smart-contracts/architecture-overview.md` | Legacy PAU vs Diamond PAU comparison table; "No new controller code for new layers" principle |
| `phase-1-pragmatic-delivery.md` §Phase 1.1 | Substage 1.1 task table (3 tasks: factory, first-cohort deployment, migration) |
| `smart-contracts/configurator-unit.md` §Extra Requirements | "Admin role prerequisites: Before a PAU can be managed through the Configurator Unit, the deployed PAU contracts must grant the Configurator the necessary admin roles" — deployment-time concern |

---

### 9. Phase 1 Manual Settlement Continuity: lpla-verify Output Contract and Inter-Team Handoff

#### 9.1 Definition (plain language)

Phase 1 continues using the existing manual settlement process — no automation. lpla-verify monitors positions and calculates CRRs but explicitly does not track settlement progress (that arrives in Phase 2 with lpla-checker). The question this spine topic addresses: what exactly does lpla-verify output that informs the manual monthly settlement calculation (the five-step methodology in `accounting/prime-settlement-methodology.md`), and which team reads those outputs to perform the settlement? Multiple GovOps teams and BA Labs are involved, but the handoff isn't specified.

#### 9.2 Why it unblocks execution

If the handoff isn't agreed before substage 1.2 (Operational Infrastructure):
- BA Labs may build the settlement calculation expecting inputs that lpla-verify doesn't provide.
- GovOps teams may wait for BA Labs to run settlement while BA Labs waits for GovOps data.
- lpla-verify outputs may be in a format that the manual settlement process can't consume without transformation.

The monthly settlement cycle is currently operating. If Phase 1 infrastructure changes disrupt it — even temporarily — the Genesis Capital targets, revenue retention requirements, and Prime interest obligations are all affected.

#### 9.3 Known ambiguities / mismatch risk

- **lpla-verify output vs settlement inputs:** The five-step settlement calculation in `prime-settlement-methodology.md` requires Average Ilk Debt, idle asset balances, Sky Direct performance data, and sUSDS spreads. lpla-verify outputs CRRs and position data. The mapping between these is not specified.
- **Who reads lpla-verify:** Outputs could be consumed by BA Labs (as calculation engine), by Atlas Axis (as Core Council GovOps), by each OEA GovOps team for their own Primes, or some combination. Four teams may all assume a different answer.
- **Data coverage gap:** Phase 1 Synome-MVP stores "position data" but the settlement calculation requires time-weighted averages over the full month. Whether lpla-verify continuously logs position snapshots, or whether settlement depends on external data, is unspecified.
- **Phase 0 assets in settlement:** Phase 0 Grove exceptional deployments are outside standardized infrastructure. How these positions feed into the settlement calculation during Phase 1 isn't addressed.

#### 9.4 What would settle it (v0)

1. Mapping from settlement five-step inputs to lpla-verify output fields (or alternative data sources).
2. Responsibility matrix: which team produces/consumes each settlement input.
3. Synome-MVP data logging requirements: position snapshot frequency and retention for time-weighted average calculation.
4. Phase 0 asset settlement procedure during Phase 1.

#### 9.5 Proposed deliverables

- **Settlement Input-Output Mapping** — maps five-step settlement inputs to lpla-verify outputs or other data sources
- **Phase 1 Settlement Responsibility Matrix** — team-by-team responsibility for each settlement step
- **Synome-MVP Logging Requirements** — what must be logged for monthly settlement calculations
- **Phase 0 Settlement Procedure** — how Phase 0 exceptional positions are handled in Phase 1 settlement

#### 9.6 Evidence

| Source | Anchor |
|--------|--------|
| `phase-1-pragmatic-delivery.md` §Executive Summary | "Phase 1 continues using the existing manual settlement process"; "lpla-verify monitors positions and calculates CRRs but does not track settlement progress" |
| `accounting/prime-settlement-methodology.md` | Five-step calculation with explicit formula for each step; "Transition Path" table (Phase 1 = monthly manual) |
| `risk-framework/sentinel-integration.md` | lpla-checker metrics table (CRR, TRRC, TRC, Encumbrance Ratio) — what lpla-verify computes |
| `roadmap/roadmap-overview.md` | "lpla-verify: monitors positions, calculates CRRs, generates alerts. Does not track settlement — that capability arrives with lpla-checker in Phase 2." |

---

## C) Sanity Checks

### C.1 Tempting but too granular — and which spine topic they belong under

| Candidate | Belongs under |
|-----------|---------------|
| Exact meaning of `totalUnderlying` field in the NFAT queue | Spine topic 1 (Synome-MVP schema) |
| Which specific Morpho vault version is supported in CoreHaloFacet | Spine topic 6 (Core Halo classification + interface) |
| Default `maxAmount` value for a Morpho USDS deposit init | Spine topic 2 (Configurator init catalog) |
| SOFR hedge declaration format | Spine topic 3 (lpla-verify data interface) |
| Who holds the Fortification Conserver multisig keys | Spine topic 5 (Term Halo legal wrapper) |
| Whether NFAT transfers are whitelisted or open for Halo1 | Spine topic 4 (NFAT Facility onboarding + interface) |
| Specific APY range values for the Halo1 buybox | Spine topic 5 (Term Halo legal infrastructure) |
| Re-attestation cadence for senior secured loans | Spine topic 7 (Attestor governance) |
| Which Prime is migrated first in substage 1.1 | Spine topic 8 (Diamond PAU migration protocol) |
| Whether lpha-council is a script or a beacon with on-chain authority | Spine topic 1 (Synome-MVP write authorization) |
| Exact gas cost estimates for Diamond PAU facets | Spine topic 8 (sub-decision within audit and deployment) |

### C.2 Phase 2+ topics intentionally excluded

The following topics were candidates but are out of Phase 1 scope:

| Topic | Why excluded |
|-------|-------------|
| lpla-checker settlement tracking | Explicitly deferred to Phase 2 |
| Monthly settlement formalization and automation | Phase 2 deliverable |
| Daily settlement lock/unlock cycle | Phase 3 deliverable |
| LCTS smart contracts and srUSDS token | Phase 4 deliverable |
| Sealed-bid OSRC and Duration auctions | Phase 9 deliverable (stl-base required) |
| Sentinel formation architecture (stl-base, stl-stream, stl-warden) | Phase 9–10 deliverables |
| Streaming Accord and carry mechanics | Phase 10 deliverable |
| Generator PAU and single-MCD ilk architecture | Phase 6 deliverable |
| Halo Factory and Prime Factory automation | Phases 5–7 deliverables |
| SpellGuard system and Guardian role consolidation | Governance transition, sequencing independent of Phase 1 infrastructure |
| Risk capital ingression mechanics (EJRC, srUSDS) | Phase 4+ (LCTS required) |

### C.3 Unknowns the repo does not yet answer

The following questions require workshops or external inputs that the corpus alone cannot resolve:

| Unknown | Needed from |
|---------|-------------|
| Who is the Attestor company for Phase 1, and what are their selection criteria? | Frontier Foundation / Core Council decision |
| Which specific Primes are in the "first cohort" for Phase 1.1 Diamond PAU migration? | GovOps teams + Core Council coordination |
| What jurisdiction and entity structure will be used for Halo Sleeves? | Frontier Foundation legal team |
| Who is the Fortification Conserver and what is their operational structure? | Frontier Foundation / Core Council decision |
| Exactly which OEA GovOps team (Amatsu, Soter, Endgame Edge, Redline) is accordant to which Prime PAUs? | All GovOps teams + Core Council |
| Which Phase 0 Grove assets will be promoted to Core Halos vs wound down? | Endgame Edge (Spark/Grove operator) + Core Council |
| What are the initial maxAmount and slope values for Core Halo inits? | BA Labs (risk framework) + Core Council approval |
| What Synome-MVP hosting infrastructure does Archon provide, and what is its availability SLA? | Archon |
| Is growth staking (in `growth-staking/growth-staking.md`) a Phase 1 deliverable or Phase 2+? | Roadmap owners — the document exists but is not referenced in the Phase 1 scope doc |

---

*This document should be treated as a starting point for workstream formation, not a final decision record. Each spine topic requires a workshop to resolve the sub-decisions listed above, with outputs in the form of v0 specs and decision records.*
