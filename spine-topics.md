# Laniakea Phase 1: Proposed Spine Topics

**Status:** Draft for team review
**Prepared by:** Claude (Archon session)
**Last Updated:** 2026-02-27

---

## Methodology note

This document was produced by reading the full `laniakea-docs/` corpus and applying the spine-topic selection process described in the task brief. `roadmap/phase-1-pragmatic-delivery.md` is treated as the authoritative scoping filter. All 9 topics either map directly to a Phase 1 deliverable or are prerequisites that block one.

**Phase 1 deliverables used as the scope filter** (from `roadmap/phase-1-pragmatic-delivery.md`):

| Substage | Deliverable |
|----------|-------------|
| 1.1 | Diamond PAU Deployment |
| 1.2 | Synome-MVP + Verify Beacon (lpla-verify) + Relay Beacon (lpha-relay) |
| 1.3 | Core Halos and Legacy Cleanup |
| 1.4 | Configurator Unit |
| 1.5 | NFAT Smart Contracts + NFAT Beacon (lpha-nfat) + First Term Halo (Halo1) |
| 1.5 (prereq) | Term Halo Legal |
| 1.6 | Term Halo Cohort (Halo2–Halo6) |

**Key Phase 1 constraints from the scope doc:**

- Phase 1 continues using the existing **manual settlement process**. lpla-verify monitors positions and calculates CRRs but does not track settlement. That capability (lpla-checker) arrives in Phase 2.
- The 14-day BEAMTimeLock delay is a **critical-path scheduling dependency**: inits and cBEAM grants must be submitted before Phase 1.4 can start. This is not just an implementation detail — it is a calendar constraint that requires early action.
- Sealed-bid OSRC and Duration auctions do not begin until after `stl-base` is deployed (Phase 9+). Phase 1 uses **manual top-down ALDM allocation**.

---

## Glossary

| Term | Plain-language definition |
|------|--------------------------|
| **Spine topic** | A cross-domain interface or constraint where ambiguity has high downstream cost, and resolving it unlocks parallel execution across multiple domains |
| **PAU** | Parallelized Allocation Unit — the standard building block (Controller + ALMProxy + RateLimits) through which capital is deployed |
| **Synome-MVP** | The Phase 1 operational database: stores risk parameters, NFAT records, and position data; serves as source of truth for all MVP beacons |
| **NFAT** | Non-Fungible Allocation Token — an ERC-721 representing a Prime's claim on a specific capital deployment in a Term Halo |
| **BEAM** | Bounded External Access Module — an on-chain authorized role. aBEAM = admin (Core Council); cBEAM = configurator (GovOps); pBEAM = process (execution) |
| **CRR** | Capital Ratio Requirement — the required risk capital per position, derived from risk weights, duration matching, and asset classification |
| **Buybox** | The set of acceptable deal parameters (duration, size, APY, counterparty, asset type) for an NFAT Facility. Deals within the buybox can be executed by lpha-nfat without governance approval |
| **Halo Sleeve** | The asset-side bankruptcy-remote container backing one or more NFATs; the isolation boundary for loss distribution |
| **lpha-nfat** | The LPHA (Low Power, High Authority) beacon that operates NFAT Facility lifecycle: claims capital from queues, mints NFATs, manages sleeve status, deploys funds, processes redemptions |
| **lpha-attest** | An independent LPHA beacon operated by an Attestor company; posts risk attestations about Halo Sleeve contents into the Synome |
| **Init** | A pre-approved rate-limit or controller-action configuration that accordant GovOps can activate for their PAUs; additions require 14-day timelock |
| **SORL** | Second-Order Rate Limit — constrains how fast rate limits can increase (default: 25% per 18 hours) |

---

## A) Candidate Spine Topics

| Rank | Spine topic | Phase relevance | 1-sentence definition | Why it's a spine topic | Primary domains/functions | Evidence (2–5 repo anchors) | Key sub-decisions (3–7) |
|------|-------------|-----------------|----------------------|------------------------|--------------------------|----------------------------|------------------------|
| 1 | **Synome-MVP: data schema, write permissions, and signed-statement protocol** | Phase 1 | Define what Synome-MVP stores, who may write to it, in what format, and how signed statements are authenticated — the shared data contract that all MVP beacons depend on. | Every MVP beacon (lpla-verify, lpha-relay, lpha-nfat, lpha-report, lpha-council) either reads or writes Synome-MVP; ambiguity in schema or write permissions causes beacon-level rework and integration failure across Archon, BA Labs, and all GovOps teams. | Archon (builds Synome + beacons), BA Labs (risk params), Atlas Axis (Core Council GovOps), Amatsu / Soter Labs / Redline (Operational GovOps) | `phase-1-pragmatic-delivery.md` §Synome-MVP data flows table; `phase-1-pragmatic-delivery.md` §What Synome-MVP Stores (3 categories); `council-beam-authority.md` Synome Write Rights table (6 write-right types); `phase-1-pragmatic-delivery.md` architecture diagram (signed statements from Core Council GovOps + Operational GovOps) | (1) Schema for risk parameters (fields, types, units); (2) Schema for NFAT records (which fields are required at claim time); (3) Position data format and update cadence; (4) Signed-statement authentication mechanism (who signs, what format); (5) Versioning and backward-compatibility policy; (6) Which beacon holds which write right at Phase 1 |
| 2 | **CRR calculation interface: risk-framework parameters to lpla-verify inputs/outputs** | Phase 1 | Define what inputs lpla-verify needs from Synome-MVP (risk params, price feeds, on-chain positions), what CRR formulas it applies, what outputs it writes back, and what constitutes an alert — the contract between BA Labs (risk framework design) and Archon (beacon implementation). | lpla-verify is live at substage 1.2 and is the only protocol-level monitoring for the rest of Phase 1; if BA Labs and Archon hold different models of what lpla-verify should compute or output, the alert infrastructure is wrong at launch with no in-Phase-1 correction mechanism. | BA Labs (risk formulas), Archon (lpla-verify implementation), Atlas Axis (consumes outputs), Core Council | `risk-framework/sentinel-integration.md` metrics table (CRR, TRRC, TRC, Encumbrance Ratio definitions); `risk-framework/capital-formula.md` (per-position formula with matched/unmatched split); `phase-1-pragmatic-delivery.md` §Verify Beacon responsibility table; `phase-1-pragmatic-delivery.md` §SOFR Hedging validation ("Primes without valid hedging…cannot deploy into duration NFATs") | (1) Input schema: which risk-param fields lpla-verify reads from Synome-MVP; (2) Price-feed sources and freshness requirements; (3) SOFR hedging validation rule: what constitutes a valid hedge declaration in Synome; (4) Output schema: CRR per position, TRRC, TRC, Encumbrance Ratio fields; (5) Alert threshold definitions (e.g., encumbrance ratio ≥90%); (6) Frequency and trigger model (event-driven vs. scheduled) |
| 3 | **Configurator Unit authority model and 14-day timelock sequencing** | Phase 1 | Agree who holds aBEAMs, which inits must be created before Phase 1.4 begins, which GovOps teams receive which cBEAMs, and when timelock timers must start — the governance scheduling contract for spell-less operations. | The 14-day BEAMTimeLock delay means init creation and cBEAM grants for Core Halos and Halo1 must be submitted ~14 days before Phases 1.4 and 1.5 need to start; if teams don't agree on this schedule, substages 1.4 and 1.5 are delayed by weeks with no workaround. | Atlas Axis (aBEAM/Core Council), Amatsu / Soter Labs / Redline (cBEAM recipients), PullUp / Dewiz / Sidestream (PAU registration), Archon | `smart-contracts/configurator-unit.md` Default Parameters table (14-day timelock, 18h hop, 25% SORL); `smart-contracts/configurator-unit.md` Invariants list (7 invariants); `governance-transition/council-beam-authority.md` BEAM Hierarchy table (4 levels, aBEAM grants cBEAM via 14-day delay); `phase-1-pragmatic-delivery.md` §Configurator Unit flow diagram (aBEAM → BEAMTimeLock → BEAMState → Configurator) | (1) Which entity holds aBEAM in Phase 1 (Core Council multisig vs. Council Beacon); (2) Init library needed before 1.4: which allocation targets (Core Halos) require inits and in what order; (3) cBEAM assignments: which GovOps team is accordant to which Prime PAU; (4) Timer start dates for 1.4 and 1.5 relative to substage start targets; (5) Emergency freeze/cancel holder in Phase 1 (any single CC Guardian per council-beam-authority.md) |
| 4 | **Diamond PAU facet interface standards and legacy migration protocol** | Phase 1 | Define which action facets exist, how the Configurator sees Diamond PAU functions, and the per-Prime migration sequence from legacy ALMProxy to Diamond — the technical interface that all Phase 1 builds depend on. | Diamond PAU is substage 1.1 and every downstream substage (Configurator, NFAT contracts, beacons) builds against it; if facet boundaries, selector routing, or the migration ordering are ambiguous, spell and Configurator builds break mid-Phase-1. | PullUp / Dewiz / Sidestream (facet dev + spell crafting), Atlas Axis (governance approval for diamondCut), Amatsu / Soter Labs / Redline (migration ops) | `phase-1-pragmatic-delivery.md` §Diamond PAU Action Facets table (NfatDepositFacet, NfatWithdrawFacet, CoreHaloFacet, LegacyMigrationFacet); `phase-1-pragmatic-delivery.md` §Migration Path (4-step sequence); `smart-contracts/diamond-pau.md` Legacy vs Diamond comparison table; `smart-contracts/diamond-pau.md` Configurator Integration section ("Diamond is transparent to Configurator") | (1) Facet boundary criteria: what logic goes in which facet; (2) Shared storage slot namespace conventions per facet to avoid collisions; (3) Migration ordering: which Primes migrate first and any ordering constraints; (4) Governance approval path for diamondCut (spell vs. aBEAM); (5) What constitutes "migration complete" for a given Prime (acceptance criteria) |
| 5 | **NFAT smart contract architecture: Facility + Redeemer contract split and pBEAM authority** | Phase 1 | Define the exact contract-level split between Facility (queue, claim, mint) and Redeemer (receive, notify, redeem), the ERC-721 onchain data model, the rate-limit configuration, and which pBEAMs lpha-nfat holds — the build contract for NFAT contracts and lpha-nfat. | NFAT smart contracts are a prerequisite for Halo1 (substage 1.5); if Archon's beacon implementation and PullUp/Dewiz/Sidestream's contract specification diverge on the Facility/Redeemer interface or pBEAM authority model, integration testing at substage 1.5 fails. | PullUp / Dewiz / Sidestream (contract dev), Archon (lpha-nfat beacon), Amatsu / Soter Labs / Redline (Halo operations) | `phase-1-pragmatic-delivery.md` §NFAT Facility table (Queue/Claim/Mint/Deploy functions); `phase-1-pragmatic-delivery.md` §NFAT Redeemer table (Receive/Notify/Redeem functions); `smart-contracts/nfats.md` ERC-721 onchain fields table (tokenId, facility, principal, depositor, mintedAt); `smart-contracts/nfats.md` Queue State table (totalShares, totalUnderlying, shares[address]); `sky-agents/halo-agents/term-halo.md` Beacon Permissions table (4 pBEAM requirements for lpha-nfat) | (1) Penalty mechanism for late Halo funding at maturity (rate vs. fixed fee, where encoded); (2) Partial redemption ("spend") mechanics for amortizing loans vs. full burn; (3) Rate limit configuration on Facility claims (maxAmount, slope per Facility); (4) pBEAM grant path for lpha-nfat (cBEAM → pBEAM, or direct aBEAM grant); (5) How multiple Facilities per Halo share vs. isolate PAU infrastructure |
| 6 | **NFAT onchain/offchain data split and Facility onboarding mechanism** | Prerequisite for Phase 1 | Define what must live onchain (custody, ownership, NFAT tokenId) vs. what lives only in Synome (deal terms, payment schedule, sleeve assignment), and how a Prime gets approved to deposit into a Facility — the semantic and governance contract the entire NFAT system rests on. | nfats.md contains an explicit open question: "The exact onboarding mechanism for Primes to Facilities is an open design question." If this is not settled before NFAT contracts are finalized, either the contracts will need post-deployment changes or lpha-nfat will execute deals without a clear authorization path. | PullUp / Dewiz / Sidestream (contract), Archon (lpha-nfat, Synome), Atlas Axis (Core Council approval), Amatsu / Soter Labs / Redline (GovOps onboarding) | `smart-contracts/nfats.md` §ERC-721 onchain fields table (5 fields) vs. §Offchain data list (6 items); `smart-contracts/nfats.md` explicit open question: "The exact onboarding mechanism…is an open design question"; `phase-1-pragmatic-delivery.md` §Deal Lifecycle step 1 ("Prime synomic governance approves deployment into NFAT Facility → Govops onboards Facility via configurator or timelock"); `smart-contracts/nfats.md` §Onboarding (open question) | (1) Minimum onchain fields: are mintedAt and depositor sufficient, or does the contract need more? (2) Synome record schema for NFAT deal terms (APY, duration, sleeve assignment — what fields, what format); (3) Prime onboarding path: is approval via Configurator init or via BEAMTimeLock? Who submits it? (4) Prime synomic governance approval: what document or on-chain action constitutes approval in Phase 1? (5) How Facility-level rate limits interact with individual NFAT claim amounts |
| 7 | **Term Halo legal framework: buybox templates, pre-signed agreements, and recourse mechanisms** | Prerequisite for Phase 1 | Establish the reusable legal templates (buybox, pre-signed Prime agreements, Fortification Conserver recourse) that allow lpha-nfat to execute deals within governance-approved bounds without per-deal legal review — the legal prerequisite for substage 1.5. | Term Halo Legal is the longest-lead deliverable in Phase 1 and must be ready before Halo1 can go live; if the buybox template or pre-signed agreements are not complete, lpha-nfat cannot execute a single deal autonomously regardless of smart contract readiness. | Frontier Foundation (legal), Atlas Axis (governance artifacts), Amatsu / Soter Labs / Redline (Halo operators), PullUp (smart contract integration for recourse) | `phase-1-pragmatic-delivery.md` §Term Halo Legal design principles table (3 principles: default ownership, standardized structures, pre-signed integration); `phase-1-pragmatic-delivery.md` §Buybox Model parameter table (Duration, Size, APY, Counterparties, Asset Types); `phase-1-pragmatic-delivery.md` §Deliverables list (Buybox Template, Pre-signed Agreements, Recourse Mechanisms, Artifact Templates); `sky-agents/halo-agents/term-halo.md` Governance Artifacts table (Halo Artifact, Unit Artifact, Sleeve Records, Synome Records) | (1) Buybox parameter ranges for Halo1 (exact Duration, Size, APY, Asset Type bounds); (2) Jurisdiction selection and regulatory framework for each Term Halo; (3) Fortification Conserver trigger conditions: what events allow takeover of Facility assets? (4) Required contents of the Halo Artifact vs. Unit Artifact (minimum viable vs. complete); (5) Pre-signing mechanics: which parties must sign before Halo1 can accept its first deposit? (6) Penalty rate for late Halo redemption funding (referenced in nfats.md but not specified) |
| 8 | **Core Halo classification criteria and data pipeline for MVP beacons** | Phase 1 | Agree on the rules for which legacy Prime exposures become Core Halos vs. are wound down, what data each Core Halo must provide to lpla-verify and lpha-relay, and whether lpha-collateral is needed — the intake contract for substage 1.3 that directly determines Configurator surface area. | Every legacy exposure left unresolved adds complexity to Configurator (more allocation targets), increases CRR calculation load, and creates more potential beacon failure modes; the cleanup criteria and data requirements must be agreed before substage 1.3 to avoid re-doing 1.3 during 1.4. | Amatsu / Soter Labs / Redline (Halo operators, know legacy assets), BA Labs (risk framework, need data for CRR), Archon (lpha-relay, lpha-collateral, lpla-verify), Atlas Axis (Core Council approval) | `phase-1-pragmatic-delivery.md` §Why Cleanup Matters table (4 impact areas); `phase-1-pragmatic-delivery.md` §Cleanup Targets (4 categories: promote/wind down/consolidate/document); `phase-1-pragmatic-delivery.md` §Acceptance Criteria for Phase 1.3 (3 conditions); `phase-1-pragmatic-delivery.md` §Collateral Beacon note ("speculative — may not be needed depending on how Core Halo data flows are structured") | (1) Classification criteria: minimum bar for "worth retaining as Core Halo" (size, liquidity, integration complexity); (2) Required fields per Core Halo Artifact (what properties, parameters, and oracle data are mandatory); (3) lpha-collateral go/no-go decision: is it needed or can data flow from existing sources? (4) Data source per legacy asset type (on-chain readable vs. requires off-chain scrape); (5) Timeline for wind-down of excluded positions relative to 1.3 start |
| 9 | **lpha-nfat + lpha-attest two-beacon deployment gate: attestation protocol and Synome write interface** | Phase 1 | Define the attestation content format, required fields, Synome write schema, and cadence that lpha-attest must post before lpha-nfat can transition a Halo Sleeve from Filling to Deploying — the interface that unlocks autonomous capital deployment and drives per-sleeve CRR. | The two-beacon gate is the mechanism that makes the Deploying/At-Rest CRR distinction enforceable; if the attestation format or Synome write permission model is unspecified, either lpha-nfat cannot act (deployment blocked) or it acts without attestation (CRR undercharged, risk control violated). | Archon (builds lpha-nfat and lpha-attest), BA Labs (defines CRR per sleeve phase), Frontier Foundation (Attestor selection/whitelisting), Amatsu / Soter Labs / Redline (Halo operators) | `smart-contracts/nfats.md` §Two-beacon deployment gate sequence diagram; `smart-contracts/nfats.md` Attestation types table (pre-deployment, post-deployment, ongoing); `smart-contracts/nfats.md` CRR Incentive Structure table (Filling/Deploying/At Rest/Missed re-attestation vs. CRR impact); `sky-agents/halo-agents/term-halo.md` §lpha-attest description ("cannot move capital, mint NFATs, change sleeve status directly") | (1) Attestor selection and governance-whitelisting process for Phase 1; (2) Attestation schema: required fields for pre-deployment and post-deployment attestations; (3) Re-attestation cadence per asset type and what triggers a CRR increase for missed re-attestation; (4) Synome write permission model for lpha-attest (scoped to which record types); (5) Two-beacon gate enforcement: is it onchain (smart contract checks Synome before allowing status change) or offchain (lpha-nfat checks Synome before submitting transaction)? |

---

## B) Detail sections

---

### Topic 1: Synome-MVP schema, write permissions, and signed-statement protocol

**Phase relevance:** Phase 1

**1. Definition (plain language)**

Synome-MVP is the single operational database that all Phase 1 beacons read from and write to. It holds three categories of data: risk parameters (signed by Core Council GovOps), NFAT records (written by lpha-nfat at deal execution), and position data (written by lpha-report). Before any beacon can be built or tested, the schema, write permissions, and signed-statement format must be specified as a shared contract.

**2. Why it unblocks execution**

- lpla-verify cannot be built until BA Labs and Archon agree on the risk-parameter field names, types, and units it will read.
- lpha-nfat cannot write NFAT records until the record schema is frozen.
- lpha-report cannot post performance summaries until the schema for CRR fulfillment and encumbrance ratio output is defined.
- lpha-council cannot update risk equations until its write permissions are scoped.
- All five MVP beacons are blocked at specification time, not just integration time.

**3. Known ambiguities / mismatch risk**

- BA Labs likely thinks in terms of formula inputs (risk weights, SPTP buckets, category caps) while Archon thinks in terms of API schema (field names, JSON vs. database records). These are different abstractions of the same content.
- "Signed statements" from Core Council GovOps are mentioned repeatedly but the signing mechanism (multisig on-chain, off-chain key, oracle) is not specified.
- Versioning policy is unspecified: if BA Labs changes a risk parameter definition, do existing beacons break?

**4. What would settle it (v0)**

A single document specifying: (a) field-level schema for each of the 3 data categories, (b) which entity holds write rights per category (mapping to the council-beam-authority.md Synome Write Rights table), (c) the signed-statement format and authentication method, (d) a versioning policy covering at minimum the Phase 1 period.

**5. Proposed deliverables**

- `synome-mvp-schema-v0.md`: field-level schema for risk params, NFAT records, and position data
- `synome-mvp-write-rights-v0.md`: maps each write right to the holding entity and the beacon that exercises it
- `synome-mvp-signed-statement-spec.md`: authentication format and verification procedure

**6. Evidence**

- `roadmap/phase-1-pragmatic-delivery.md`, §Synome-MVP Data Flows table: "Core Council GovOps → signed risk parameter updates → lpla-verify (for CRR calculations)"
- `roadmap/phase-1-pragmatic-delivery.md`, §What Synome-MVP Stores: three explicit categories (risk parameters, NFAT records, position data)
- `governance-transition/council-beam-authority.md`, Synome Write Rights table: 6 distinct write-right types with different grant chains
- `roadmap/phase-1-pragmatic-delivery.md`, Synome-MVP architecture diagram: shows both Core Council GovOps and Operational GovOps as writers; lpla-verify and lpha-nfat as readers

---

### Topic 2: CRR calculation interface — risk-framework parameters to lpla-verify inputs/outputs

**Phase relevance:** Phase 1

**1. Definition (plain language)**

lpla-verify is the Phase 1 monitoring system. It takes risk parameters from Synome-MVP, on-chain position data, and price feeds; applies the capital formula; and produces CRR per position, TRRC, TRC, and Encumbrance Ratio. It also validates SOFR hedging declarations. This topic defines the precise inputs, calculation rules, outputs, and alert thresholds — the interface between BA Labs (who owns the risk framework) and Archon (who builds lpla-verify).

**2. Why it unblocks execution**

lpla-verify goes live at substage 1.2, before Core Halos, Configurator, and NFAT contracts exist. If the calculation interface is wrong at launch, every CRR report in Phase 1 is unreliable with no in-Phase correction path (lpla-checker, the corrected replacement, is Phase 2). The encumbrance ratio enforcement mechanism (`risk-monitoring.md`: "target ≤90%") cannot be operationalized until the output schema is agreed.

**3. Known ambiguities / mismatch risk**

- The capital formula (`capital-formula.md`) has multiple branches (matched/unmatched, pull-to-par, gap risk, category caps) that require different inputs. Archon may implement a subset; BA Labs may expect the full formula from day one.
- The SOFR hedging validation rule says Primes "cannot deploy into duration NFATs via the ALDM system" without a valid hedge — but what a "valid hedge declaration" looks like in Synome is not specified.
- Encumbrance ratio enforcement ("rate limit reduction, new deployment freeze") is marked "governance proposal required" in `risk-monitoring.md`. It is unclear if lpla-verify Phase 1 must support automated enforcement or only alerting.
- Price feed sources and freshness requirements are not listed.

**4. What would settle it (v0)**

A two-part spec: (a) an input manifest (which Synome fields lpla-verify reads, which price feeds it consumes, and their freshness SLA), and (b) an output schema (field names and definitions for CRR, TRRC, TRC, Encumbrance Ratio, and alert records written to Synome). SOFR hedging validation rule needs a concrete Synome field spec.

**5. Proposed deliverables**

- `lpla-verify-interface-v0.md`: input manifest + output schema + alert threshold table
- Decision record: Phase 1 enforcement model (alerting-only vs. automated rate-limit reduction)
- `sofr-hedge-declaration-spec.md`: what a valid hedge declaration looks like in Synome-MVP

**6. Evidence**

- `risk-framework/sentinel-integration.md`, Key Metrics table: CRR, TRRC, TRC, Encumbrance Ratio definitions
- `risk-framework/capital-formula.md`: per-position formula with matched/unmatched/gap-risk branches
- `roadmap/phase-1-pragmatic-delivery.md`, §Verify Beacon responsibility table: Position Verification / CRR Calculation / Alert Generation
- `roadmap/phase-1-pragmatic-delivery.md`, §SOFR Hedging: "lpla-verify validates that Primes deploying into duration NFATs have declared either: Hedge positions covering the interest rate exposure [or] SOFR plus terms on the underlying NFAT"
- `risk-framework/risk-monitoring.md`, §Encumbrance Ratio Enforcement: "governance proposal required"

---

### Topic 3: Configurator Unit authority model and 14-day timelock sequencing

**Phase relevance:** Phase 1 / Prerequisite for Phase 1

**1. Definition (plain language)**

The Configurator Unit uses a two-tier model: aBEAMs (held by Core Council) create inits and grant cBEAMs; cBEAMs (held by GovOps teams) set rate limits and manage PAU operations. All additions — init creation, cBEAM grants, PAU registration — require a 14-day timelock. Removals are instant. This spine topic defines: who holds which roles, what inits must be created, which GovOps teams receive which cBEAMs, and when those timers must start relative to Phase 1 substage dates.

**2. Why it unblocks execution**

- Phase 1.4 cannot start until aBEAMs have created Core Halo inits AND granted cBEAMs to GovOps teams, AND those 14-day timers have elapsed.
- Phase 1.5 cannot start until Halo1-specific inits and cBEAMs are through the timelock.
- If teams plan these substages without accounting for the 14-day lead time, they will discover the sequencing issue after contracts are deployed but before operations can begin — a gap with no workaround.

**3. Known ambiguities / mismatch risk**

- The Phase 1 scope doc shows aBEAMs as "Core Council" but `council-beam-authority.md` describes the full target model where aBEAMs are set via the Council Beacon (HPHA). It is unclear which model is active at Phase 1 launch — this affects who submits the timelock transactions.
- cBEAM assignments (which GovOps team is accordant to which Prime PAU) are not specified. Different GovOps teams may have different expectations about their scope.
- The init library for Core Halos is not enumerated. Teams do not know how many init transactions need to be submitted, or in what order.

**4. What would settle it (v0)**

A single scheduling document: (a) list of inits needed for all Phase 1.4 Core Halo targets and Phase 1.5 Halo1, (b) cBEAM assignment map (GovOps team → PAUs), (c) PAU registration list, (d) proposed timer start dates back-calculated from substage target dates, (e) who submits each timelock transaction. This is a planning artifact, not a spec — but planning without it is impossible.

**5. Proposed deliverables**

- `configurator-onboarding-schedule-v0.md`: timelock submission calendar for Phase 1.4 and 1.5
- `cbeam-assignment-map-v0.md`: GovOps team → accordant PAUs for Phase 1
- `init-library-phase1-v0.md`: enumerated inits needed for Core Halos and Halo1

**6. Evidence**

- `smart-contracts/configurator-unit.md`, Default Parameters: "Timelock delay: 14 days"
- `smart-contracts/configurator-unit.md`, Invariants: "No configuration without init", "Accordant check", "Timelock on additions"
- `governance-transition/council-beam-authority.md`, BEAM Hierarchy table (4 levels, additions timelocked 14 days, removals instant)
- `roadmap/phase-1-pragmatic-delivery.md`, §Phase 1.4 tasks: "Register Core Halos: Add Core Halo allocation targets to Configurator" + "Grant cBEAMs: Provision GovOps teams for their respective PAUs"

---

### Topic 4: Diamond PAU facet interface standards and legacy migration protocol

**Phase relevance:** Phase 1

**1. Definition (plain language)**

Diamond PAU replaces the legacy single-controller design with a proxy that routes calls to modular facets. Phase 1 defines four required facets (NfatDepositFacet, NfatWithdrawFacet, CoreHaloFacet, LegacyMigrationFacet). This topic defines the facet boundaries, the shared storage namespacing convention, how the Configurator and beacons see the Diamond interface, and the per-Prime migration sequence from legacy ALMProxy.

**2. Why it unblocks execution**

- Diamond PAU is substage 1.1. Configurator unit integration, lpha-relay beacon builds, and NFAT facet development all build against the Diamond interface.
- If facet boundaries are ambiguous, selector collisions or shared-storage conflicts are only discovered at integration time.
- The migration path (4 steps per Prime) requires a sequencing agreement between contract developers (who deploy) and GovOps teams (who execute migration ops and hold cBEAMs). Without this, migrations happen ad hoc and the legacy wind-down in 1.3 is disorganized.

**3. Known ambiguities / mismatch risk**

- `smart-contracts/diamond-pau.md` says "Configurator Integration: the diamond is transparent to the Configurator — it just sees callable functions." This may not be true if facet-specific access control differs from the legacy Controller model.
- The diamondCut governance path (spell vs. aBEAM via Configurator) is not specified. Two different teams may assume different paths.
- The facet list in Phase 1 is defined in `phase-1-pragmatic-delivery.md` but `smart-contracts/diamond-pau.md` lists a different (broader) set. Teams need a canonical Phase 1 facet list.

**4. What would settle it (v0)**

A facet specification document: (a) canonical Phase 1 facet list with function signatures, (b) shared storage namespace convention per facet, (c) diamondCut authorization path, (d) acceptance criteria per Prime migration (what "migration complete" means in verifiable terms), (e) migration ordering plan.

**5. Proposed deliverables**

- `diamond-pau-facet-spec-phase1-v0.md`: Phase 1 facets, function signatures, storage namespaces
- `diamond-pau-migration-plan.md`: per-Prime migration sequence and acceptance criteria

**6. Evidence**

- `roadmap/phase-1-pragmatic-delivery.md`, §Phase 1 Action Facets table (NfatDepositFacet, NfatWithdrawFacet, CoreHaloFacet, LegacyMigrationFacet)
- `roadmap/phase-1-pragmatic-delivery.md`, §Migration Path: 4-step sequence (Deploy → Migrate positions → Grant cBEAMs → Wind down legacy)
- `smart-contracts/diamond-pau.md`, §Facet Organization table (ERC4626Facet, SwapFacet, MorphoFacet, NFATFacet, AdminFacet)
- `smart-contracts/diamond-pau.md`, §Configurator Integration: "transparent to the Configurator"
- `smart-contracts/diamond-pau.md`, §Storage: namespaced storage pattern (keccak256 slot per facet)

---

### Topic 5: NFAT smart contract architecture — Facility + Redeemer contract split and pBEAM authority

**Phase relevance:** Phase 1

**1. Definition (plain language)**

NFAT smart contracts consist of two components: the Facility (holds the queue, executes claims, mints ERC-721 NFATs) and the Redeemer (holds returned funds, allows NFAT holders to burn for principal + yield). This topic defines the contract-level interface — the ERC-721 data model, the queue share accounting, the rate-limit configuration per Facility, and exactly which pBEAMs lpha-nfat must hold to operate each component.

**2. Why it unblocks execution**

NFAT contracts are the core deliverable of substage 1.5. Archon's lpha-nfat beacon implementation and PullUp/Dewiz/Sidestream's contract specification are built in parallel; if they diverge on the Facility/Redeemer interface, pBEAM model, or penalty mechanics, substage 1.5 integration testing fails. The penalty mechanism for late Halo funding is mentioned in multiple docs but not specified numerically anywhere — it affects legal negotiations and Prime expectations.

**3. Known ambiguities / mismatch risk**

- `smart-contracts/nfats.md` §Integration Notes: "lpha-nfat beacon integration: TBD" — the integration spec is explicitly unresolved.
- The penalty mechanism for late Halo funding is referenced in `phase-1-pragmatic-delivery.md` and `nfats.md` but the rate or structure is not specified.
- The payment-delivery section of nfats.md says extended flows are "intentionally left undefined" — this is intentional for the standard but must be bounded for Phase 1 contracts.
- Multiple Facilities per Halo: nfats.md says each Facility has its own PAU, but Phase 1 deploys Halo1-Halo6. The PAU proliferation implications for Configurator surface area are not modeled.

**4. What would settle it (v0)**

An NFAT contract spec: (a) ERC-721 onchain field list (from nfats.md) confirmed as canonical for Phase 1, (b) Facility vs. Redeemer function interface (inputs, outputs, events), (c) pBEAM requirements per lpha-nfat action, (d) penalty rate for late funding, (e) Phase 1 payment patterns supported (bullet loan confirmed; amortizing TBD).

**5. Proposed deliverables**

- `nfat-contract-spec-phase1-v0.md`: Facility + Redeemer interface, ERC-721 model, pBEAM requirements
- Decision record: penalty structure for late Halo funding
- Decision record: Phase 1 payment patterns in scope (bullet only, or also amortizing)

**6. Evidence**

- `roadmap/phase-1-pragmatic-delivery.md`, §NFAT Facility table (Queue/Claim/Mint/Deploy)
- `roadmap/phase-1-pragmatic-delivery.md`, §NFAT Redeemer table (Receive/Notify/Redeem)
- `smart-contracts/nfats.md`, ERC-721 onchain fields table (5 fields)
- `smart-contracts/nfats.md`, Queue State table (totalShares, totalUnderlying, shares[address])
- `sky-agents/halo-agents/term-halo.md`, Beacon Permissions table (4 pBEAM requirements for lpha-nfat)
- `smart-contracts/nfats.md`, §Integration Notes: "TBD — lpha-nfat beacon integration"

---

### Topic 6: NFAT onchain/offchain data split and Facility onboarding mechanism

**Phase relevance:** Prerequisite for Phase 1

**1. Definition (plain language)**

The NFAT design splits data: the ERC-721 holds only custody and ownership fields; deal terms (APY, duration, sleeve assignment, payment schedule) live in Synome-MVP. This topic resolves two related ambiguities: (a) exactly which fields are required onchain vs. optional Synome fields, and (b) how a Prime is authorized to deposit into a Facility — a question nfats.md flags as explicitly open.

**2. Why it unblocks execution**

The onchain/offchain split defines the scope of the NFAT smart contract and the Synome schema simultaneously. Until it is resolved, neither the contracts nor the Synome record spec can be finalized. The onboarding mechanism ambiguity is more acute: if the onboarding path is Configurator (init + cBEAM), it must be in the timelock schedule from Topic 3; if it is BEAMTimeLock directly, it requires a different process. Resolving this unblocks the Phase 1 scheduling work.

**3. Known ambiguities / mismatch risk**

- nfats.md states the onboarding mechanism is "an open design question" — this is the clearest hard-anchor evidence of unresolved ambiguity in the corpus.
- "Prime synomic governance approves the Facility" is mentioned in the deal lifecycle but the mechanism is undefined in Phase 1 (Synome governance is a later-phase concept).
- Some teams may assume deal terms can always be reconstructed from onchain events; others may assume Synome is required to derive the NFAT's value at all.

**4. What would settle it (v0)**

Two decisions: (a) a canonical onchain/offchain field assignment for Phase 1 NFAT contracts (freeze the ERC-721 field list; enumerate mandatory Synome fields at claim time), and (b) a Prime onboarding decision record (Configurator init path or BEAMTimeLock path, with ownership of the approval transaction).

**5. Proposed deliverables**

- Decision record: Phase 1 onchain vs. Synome field assignment for NFAT positions
- Decision record: Prime-to-Facility onboarding mechanism and approval path
- `nfat-synome-record-schema-v0.md`: required Synome fields at deal execution

**6. Evidence**

- `smart-contracts/nfats.md`, §ERC-721 onchain fields table (5 fields: tokenId, facility, principal, depositor, mintedAt) vs. §Offchain data list (6 items: APY, duration, payment schedule, maturity, sleeve assignment, sleeve contents)
- `smart-contracts/nfats.md`, §Onboarding: "The exact onboarding mechanism for Primes to Facilities is an open design question"
- `roadmap/phase-1-pragmatic-delivery.md`, §Deal Lifecycle step 1: "Prime synomic governance approves deployment into NFAT Facility → Govops onboards Facility via configurator (rate limits) or timelock (BEAMstate)"

---

### Topic 7: Term Halo legal framework — buybox templates, pre-signed agreements, and recourse mechanisms

**Phase relevance:** Prerequisite for Phase 1

**1. Definition (plain language)**

A Term Halo requires legal infrastructure before lpha-nfat can execute any deal: a buybox template defining acceptable deal parameters, pre-signed agreements from participating Primes, and defined recourse mechanisms for the Fortification Conserver in case of default. These are not smart contract components — they are legal documents that must be completed before Halo1 accepts its first deposit.

**2. Why it unblocks execution**

The Term Halo legal framework is explicitly listed as a Phase 1 deliverable and is the longest-lead item in substage 1.5. It has no technical dependency order — it can be started immediately — but it cannot be parallelized away: without a signed buybox and Prime agreements, lpha-nfat is technically operational but legally prohibited from executing any deal. Since legal drafting, review, and signature collection take weeks, this must start before smart contract development completes.

**3. Known ambiguities / mismatch risk**

- The buybox parameter ranges for Halo1 are shown as examples in `phase-1-pragmatic-delivery.md` (6–24 months, 5M–100M, 8–15% APY) but are explicitly marked as illustrative, not final.
- The "Fortification Conserver" is referenced as the recourse mechanism but its identity, trigger conditions, and operational authority in Phase 1 are not described anywhere in the corpus.
- Jurisdiction selection for Term Halos is unspecified. Different jurisdictions affect both the legal template design and the asset types in scope.
- The penalty structure for late Halo funding at maturity is referenced legally but not quantified.

**4. What would settle it (v0)**

For Halo1: (a) a finalized buybox with specific parameter ranges, counterparty list, and asset type constraints; (b) a standard Prime participation agreement (the pre-signed form); (c) a recourse procedure document with Fortification Conserver trigger conditions; (d) a Halo Artifact template covering minimum required contents. These are legal artifacts, not technical specs.

**5. Proposed deliverables**

- `halo1-buybox-v0.md` (or legal equivalent): final parameter ranges, jurisdiction, asset types
- `prime-participation-agreement-template-v0`: standard form for Phase 1 Primes
- `recourse-procedure-v0.md`: Fortification Conserver triggers, authority, and contact
- `halo-artifact-template-v0.md`: minimum required contents of Halo Artifact and Unit Artifact

**6. Evidence**

- `roadmap/phase-1-pragmatic-delivery.md`, §Term Halo Legal design principles table (3 principles)
- `roadmap/phase-1-pragmatic-delivery.md`, §Buybox Model parameter table (Duration, Size, APY, Counterparties, Asset Types with example ranges)
- `roadmap/phase-1-pragmatic-delivery.md`, §Deliverables list (Buybox Template, Pre-signed Agreements, Recourse Mechanisms, Artifact Templates)
- `sky-agents/halo-agents/term-halo.md`, §Legal Infrastructure / Governance Artifacts table (Halo Artifact, Unit Artifact, Sleeve Records, Synome Records)

---

### Topic 8: Core Halo classification criteria and data pipeline for MVP beacons

**Phase relevance:** Phase 1

**1. Definition (plain language)**

Before substage 1.3, teams must agree on the rules for deciding which legacy Prime exposures are retained as Core Halos (standardized Halo Units) vs. wound down, what data each Core Halo must supply to lpla-verify and lpha-relay, and whether lpha-collateral (marked speculative in the Phase 1 doc) is needed to carry that data or whether existing data flows suffice.

**2. Why it unblocks execution**

Phase 1.4 cannot register Core Halos in Configurator until 1.3 has produced a final list with complete Halo Unit Artifacts. The Configurator init count depends directly on how many Core Halos survive. lpha-relay cannot be built to handle Core Halo deployments until the data format per asset type is defined. If lpha-collateral is needed, it must be built; if not, that build is wasted work. Both decisions must be made early to avoid re-scoping mid-phase.

**3. Known ambiguities / mismatch risk**

- The Phase 1 doc lists cleanup targets (promote/wind down/consolidate/document) without specific criteria for "worth retaining." Different GovOps teams may apply different standards to their legacy positions.
- lpha-collateral is marked "speculative — may not be needed depending on how Core Halo data flows are structured." Different teams may be planning different pipelines for the same data.
- The data requirements for lpla-verify per Core Halo type (on-chain DeFi vault vs. RWA position) are structurally different; the monitoring cadence and data freshness requirements may not be the same.

**4. What would settle it (v0)**

A classification decision record: (a) explicit retention criteria (size floor, integration complexity ceiling, oracle availability), (b) enumerated list of Phase 1 Core Halos (agreed by relevant GovOps teams and Core Council), (c) mandatory data fields per Core Halo Artifact, (d) lpha-collateral go/no-go decision with rationale.

**5. Proposed deliverables**

- `core-halo-classification-criteria-v0.md`: retention rules and application to known legacy exposures
- `core-halo-artifact-template-v0.md`: required properties, parameters, and oracle data fields
- Decision record: lpha-collateral needed/not needed and data source per asset type

**6. Evidence**

- `roadmap/phase-1-pragmatic-delivery.md`, §Why Cleanup Matters table (4 impact areas: Configurator, MVP Beacons, Risk Framework, Operations)
- `roadmap/phase-1-pragmatic-delivery.md`, §Cleanup Targets (4 categories)
- `roadmap/phase-1-pragmatic-delivery.md`, §Acceptance Criteria for Phase 1.3 (3 conditions: Core Halos defined, no orphan exposures, consistent interfaces)
- `roadmap/phase-1-pragmatic-delivery.md`, §Collateral Beacon note: "speculative — may not be needed depending on how Core Halo data flows are structured"

---

### Topic 9: lpha-nfat + lpha-attest two-beacon deployment gate — attestation protocol and Synome write interface

**Phase relevance:** Phase 1

**1. Definition (plain language)**

Before lpha-nfat can transition a Halo Sleeve from Filling to Deploying, an independent Attestor must post a pre-deployment attestation into Synome via lpha-attest. A post-deployment attestation is required to transition to At Rest. Ongoing re-attestations drive CRR adjustments. This topic defines the attestation content schema, Synome write permissions for lpha-attest, the two-beacon gate enforcement mechanism, and the Attestor selection and whitelisting process.

**2. Why it unblocks execution**

The two-beacon gate is the mechanism that makes the Deploying/At-Rest CRR distinction enforceable. Without a defined attestation schema, Archon cannot build lpha-attest, and the CRR model (`smart-contracts/nfats.md` CRR Incentive Structure table) has no operational backing. The Attestor must be selected and whitelisted by Sky governance before Halo1 can deploy any capital — this is a governance action with a lead time that must be accounted for in the Phase 1.5 schedule. The Attestor also does not appear in the teams appendix, which means their selection and whitelisting process is an open organizational question.

**3. Known ambiguities / mismatch risk**

- The Attestor is described as "a company whitelisted by Sky governance" but no candidate or selection process is described in the corpus. Teams likely hold different assumptions about who this is or when the selection happens.
- The attestation content fields are described narratively ("risk characteristics, timeframe, legal confirmation") but no schema exists. BA Labs and Archon may operationalize these differently.
- The two-beacon gate enforcement is described at the process level (attestation must be present before lpha-nfat can act) but the enforcement mechanism — whether it is enforced by Synome access control, by a smart contract check, or by lpha-nfat's internal logic — is not specified.
- Re-attestation cadence is "per asset type" but no asset-type-specific cadences are defined.

**4. What would settle it (v0)**

A two-part output: (a) attestation schema (required fields for pre-deployment and post-deployment attestations, expressed as Synome record fields), and (b) two-beacon gate enforcement decision (onchain check vs. Synome access control vs. beacon-enforced). The Attestor selection governance process needs to be on the Phase 1.5 critical path with a defined owner.

**5. Proposed deliverables**

- `lpha-attest-schema-v0.md`: attestation record fields, required vs. optional, per attestation type
- Decision record: two-beacon gate enforcement mechanism
- `attestor-whitelisting-process-v0.md`: governance steps and lead time to whitelist an Attestor for Halo1
- Decision record: re-attestation cadence for Phase 1 asset types

**6. Evidence**

- `smart-contracts/nfats.md`, §Two-beacon deployment gate sequence diagram
- `smart-contracts/nfats.md`, Attestation types table (pre-deployment, post-deployment, ongoing re-attestation)
- `smart-contracts/nfats.md`, CRR Incentive Structure table (Filling/Deploying/At Rest/Missed re-attestation vs. CRR impact)
- `sky-agents/halo-agents/term-halo.md`, §lpha-attest: "The Attestor is a company whitelisted by Sky governance"

---

## C) Sanity checks

### Tempting but too granular — rolled into larger spine topics

| Granular item | Belongs under |
|---------------|---------------|
| Exact `tokenId` assignment convention for NFATs | Topic 5: NFAT smart contract architecture |
| Specific CRR weight per asset class (e.g., Morpho vault vs. senior secured RWA) | Topic 2: CRR calculation interface |
| SORL parameters (25% per 18h default) | Topic 3: Configurator authority model |
| Exact 14-day timelock delay value | Topic 3: Configurator authority model |
| Halo1 specific partner identity | Operational, not a spine topic; belongs in Phase 1.0 planning |
| Specific field: `target_node` in a transaction log | Topic 1: Synome-MVP schema (sub-decision under schema) |
| PAU registration function signature (`setPauContracts`) | Topic 4: Diamond PAU / Topic 3: Configurator (sub-decision) |
| Penalty rate for late Halo redemption funding | Topic 5: NFAT contracts (sub-decision); Topic 7: legal framework (sub-decision) |
| Sleeve privacy: how many assets to blend per sleeve | Operational heuristic for the Halo operator; not a spine topic |

### Phase 2+ topics intentionally excluded

| Topic | Why excluded |
|-------|--------------|
| Monthly settlement cycle obligations and deadlines (lpla-checker) | Phase 1 explicitly continues manual settlement; lpla-checker is Phase 2 |
| Settlement tracking and CRR settlement-window calculations | Same as above |
| Sealed-bid OSRC and Duration auctions | Phase doc: "begin later once Prime-side `stl-base` is deployed" (Phase 9+) |
| Daily settlement cycle | Phase 3 target per phase-1-pragmatic-delivery.md |
| LCTS / Portfolio Halo standard | Not a Phase 1 deliverable; no Portfolio Halo infrastructure in Phase 1 |
| Risk capital ingression curves (EJRC/SRC/MC-based) | Interesting but does not block any Phase 1 deliverable |
| stl-base / stl-warden sentinel formations | Phase 9–10; Phase 1 uses only low-power beacons |
| Generator PAU and Generator Factory | Phase 6–8 |
| Synome full governance (ossification, crystallization) | Phase 2+; Phase 1 uses Synome-MVP only |
| LCTS queue generations and batch settlement | Phase 2+ (Portfolio Halos) |

### Unknowns the repo does not yet answer

These will require workshops or external inputs before the corresponding spine topics can be settled:

1. **Attestor identity and selection**: Who is the Attestor for Phase 1? The corpus says "a company whitelisted by Sky governance" but names no candidate and specifies no selection process. This is a prerequisite for the Phase 1.5 schedule and must be resolved in Phase 1.0 planning.

2. **Synome-MVP technology stack**: The repo describes Synome-MVP functionally (accepts signed statements, serves as source of truth) but does not specify the implementation (database type, API protocol, authentication method). Archon owns this, but external teams need the API contract to build against it.

3. **First Prime cohort**: Phase 1.0 planning is supposed to identify "first cohort of Primes participating in initial NFAT deployments." This is not specified. The cohort selection directly determines how many Diamond PAU migrations, Configurator inits, and cBEAM grants are needed in substages 1.1–1.4.

4. **Core Halo enumeration**: The specific legacy assets that will become Core Halos are not listed. The cleanup criteria (Topic 8) must be applied to a real list to produce actionable init and cBEAM requirements.

5. **Fortification Conserver identity and authority**: Referenced as the recourse mechanism for Term Halos but not described operationally. Legal framework work (Topic 7) cannot finalize recourse procedures without knowing who this is and what authority they hold in Phase 1.

6. **Signed-statement authentication**: The Synome-MVP architecture shows "signed statements" from Core Council GovOps but the signing mechanism (multisig, threshold signature, oracle attestation) is not specified. This is a security-critical choice for Topic 1.

7. **Two-beacon gate enforcement location**: Whether the "attestation must exist before lpha-nfat acts" rule is enforced by a smart contract, by Synome access control, or purely by lpha-nfat's own logic has security implications that different teams will resolve differently if not explicitly decided.
