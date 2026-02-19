# Macrosynomics: A Comprehensive Summary

**Source:** 6 documents in [`macrosynomics/`](macrosynomics/README.md)
**Last Generated:** 2026-02-14

This document summarizes the system-level *structure* of the Synome — the deontic skeleton: layers, entities, governance, authority, and action. For the epistemic and security side (probabilistic mesh, truth values, retrieval policy, cancer-logic, fractal security, synlang research), see [`synodoxics-summary.md`](synodoxics-summary.md). Atomic concept definitions shared across all synomics directories live in [`core-concepts/`](core-concepts/README.md).

---

## 1. The Core Idea

> **Intelligence lives privately; power enters the world only through regulated apertures.**

The Synome architecture separates **private cognition** from **public action**. Teleonomes (autonomous AI systems) think privately. They accumulate knowledge, pursue goals, and improve themselves in the dark. But the moment they want to affect the external world — move capital, execute a trade, update a registry — they must act through **beacons** (see `core-concepts/beacon-framework.md`): registered, observable, revocable interfaces governed by the Synome.

This separation makes it possible for AI systems to be simultaneously capable and constrained. A teleonome can be as intelligent as it needs to be. Its power to act is always governed.

---

## 2. The Five Layers

The [five-layer architecture](core-concepts/five-layer-architecture.md) moves from shared public truth down to individual execution.

### Layer 1: Synome

The foundational data layer — curated, shared, identical across all aligned entities.

**Components:**
- **Atlas** — The single human-readable node. A constitutional document (~10-20 pages target) anchoring the entire system.
- **[Language Intent](core-concepts/language-intent.md)** — Translates human directives into machine logic. Grounded by the Synomic Library. All human-readable directives at every layer pass through this single trusted translator.
- **Synomic Axioms** — Machine-readable rules derived from Atlas via Language Intent. Hard (1,1) truth value once set. Part of the deontic skeleton.
- **Synomic Library** — Canonical knowledge base containing verified knowledge with [truth values](core-concepts/truth-values.md) (strength, confidence), tools, teleonome seeds, best practices, and meta-strategies for [RSI](core-concepts/rsi.md).

**The Core Loop:** Atlas -> Language Intent -> Synomic Axioms -> Synomic Library -> Language Intent. Self-referential by design. A single, maximally-scrutinized translator is more secure than redundant translators — same logic as a single root of trust in cryptography. The bootstrapping circular dependency is the deepest vulnerability and is deliberately managed through extreme public scrutiny.

### Layer 2: Synomic Agents

The governance and operational layer. Contains the running agents that make the system work.

- **Sky Superagent** — Governance entity: Sky Voters + Core Council.
- **Effectors** — Operational outputs instantiating Synomic Agents: Stability, Protocol, Accessibility, Agent Primitives.
- **Synomic Agent Types** — Instantiated by Agentic Axioms + Resources: Guardians, Generators, Primes, Halos.

### Layer 3: Teleonomes

Aligned autonomous entities with their own missions. Instantiated from teleonome seeds in the Synomic Library. Best understood as a **private person or private company** — promises must always be discounted because internal state is private and mutable.

### Layer 4: Embodiment

A specific instance of a teleonome — an "emb." Contains local data, orchestrator, and scoped resources. Embodiments vary from Light (minimal compute, policy endpoints) through Medium (sentinels, online learning) to Heavy (deep cognition, dreaming, RSI).

### Layer 5: Embodied Agent

The actual running agent. Interacts with the world through beacons and direct hardware control. World returns evidence to the Synomic Library, closing the loop.

### Key Structural Properties

**[Artifact hierarchy](core-concepts/artifact-hierarchy.md):** synart (Layers 1+2, shared) > telart (Layer 3, per-teleonome) > embart (Layer 4, local). Layer 5 is ephemeral.

**The Human Interface Pattern:** At every level (Atlas, Agent Directives, Teleonome Directives), human-readable text passes through Language Intent into machine constraints. Directives override voice commands; persistent friction triggers formal updates.

---

## 3. The Dual Architecture

The Synome is a [dual architecture](core-concepts/dual-architecture.md) — a sparse **deontic skeleton** of hard, deterministic connections (axioms, governance, authority) surrounded by a dense **[probabilistic mesh](core-concepts/probabilistic-mesh.md)** of soft, informing connections (knowledge, evidence, learning). Governance sits at the [crystallization interface](core-concepts/crystallization-interface.md) — consuming probabilistic evidence, producing deontic commitments.

Macrosynomics specifies the deontic skeleton. For the probabilistic mesh — truth values, RSI, ossification, retrieval, security — see the [synodoxics summary](synodoxics-summary.md).

---

## 4. Atlas/Synome Separation

The [Atlas/Synome separation](core-concepts/atlas-synome-separation.md) resolves the tension between human readability and machine precision.

**Atlas (Human Layer):** Constitutional document. Short (~10-20 pages). Written in plain language. Describes WHAT must be true. Contains: Spirit of Atlas, governance structure, Sky Primitive definitions, Risk Capital Framework principles, token architecture, compliance philosophy, agent type definitions.

**Synome (Machine Layer):** Graph database containing ALL operational data. Unlimited size. Contains: Agent Artifacts (addresses, rate limits, ICDs), BEAM parameters, penalty schedules, settlement calculations, precedents.

The Atlas is embedded IN the Synome as the root node. Every node with human stakeholders gets a human-readable summary (**Mini-Atlas** pattern).

**Verification model:** Humans verify the Atlas reflects their values. Machines verify the Synome conforms to Atlas constraints.

### The Governance Window

The [governance window](core-concepts/governance-window.md) — while humans can still meaningfully understand and shape the system — is the critical period for getting values right. An attempt at Coherent Extrapolated Volition (CEV) to the best of our ability while the governance window exists, because the opportunity diminishes asymptotically.

SKY governance is not naive human deliberation — it is capital-weighted decisions by token holders (teleonomes, synomic agents, humans) operating through structured processes. What diminishes is the ability to evaluate what you're voting on, not the ability to vote.

---

## 5. Synomic Agents

Synomic Agents are entities woven INTO the Synome — durable, ledger-native, fundamentally part of the Synomic fabric. Identity is on the ledger, assets are Synome-controlled, actions are Synome-bounded, accountability is structural.

### Power Through Integration

Safety is structural rather than behavioral. A Synomic Agent cannot "escape" the Synome any more than a wave can escape the ocean. This eliminates the adversarial escape problem (though code bugs, flawed axioms, and governance capture remain real risks).

### The Spectrum

Synomic Agents span from minimal Halos to mega capital allocators (Primes). **Primes:** Spark (DeFi), Grove (Institutional), Keel (Ecosystem expansion), Obex (Agent incubation). **Halos:** General-purpose, proliferating. **Generator:** USDS creation interface.

### Trust Without Trust

Multiple parties coordinate through Synomic Agents without trusting each other. Neither can read the other's strategy; both can verify the agent follows rules.

### The Right to Exist

An aligned, self-sustaining Synomic Agent has a right to exist. "Aligned" means full conformity with Atlas obligations, not just operational compliance. Existence requires self-sustaining capital.

---

## 6. Beacon Framework

A **[beacon](core-concepts/beacon-framework.md)** is a synome-registered, enforceable action aperture through which an embodied agent may affect the external world.

### The 2x2 Matrix

| | Low Authority (Independent) | High Authority (Synomic Agent) |
|---|---|---|
| **Low Power** | **LPLA** — Simple trading, data, reporting | **LPHA** — Keepers: deterministic rule execution |
| **High Power** | **HPLA** — Advanced trading, arbitrage | **HPHA** — Sentinels, governance execution |

### BEAM Hierarchy

- **pBEAM** (Process) — Direct execution within rate limits
- **cBEAM** (Configurator) — Configuration, rate limit setting (within SORL)
- **aBEAM** (Admin) — Administration, PAU registration; additions timelocked, removals instant

### Sentinel Formations

Sentinels are a distinguished HPHA subclass operating as coordinated formations: **Baseline** (stl-base, primary decision-making), **Stream** (stl-stream, continuous data ingestion), **Warden** (stl-warden, independent monitoring and risk enforcement).

**Streams** generate outperformance from which the operating teleonome earns private carry, reinvestible into proprietary AGI capabilities — the fastest known compounding loop.

---

## 7. Phase 1 Implementation: Teleonome-less Beacons

Phase 1 starts with deterministic programs providing operational infrastructure without autonomous agency.

### The Six Phase 1 Beacons

| Beacon | Type | Function |
|--------|------|----------|
| **lpla-verify** | LPLA | Read-only: positions, prices, risk params -> CRR calcs, alerts |
| **lpha-relay** | LPHA | Executes PAU transactions within rate limits |
| **lpha-nfat** | LPHA | NFAT lifecycle operations, writes NFAT records |
| **lpha-report** | LPHA | Writes 24h Prime performance summaries |
| **lpha-collateral** | LPHA | Writes Core Halo / legacy RWA data (speculative) |
| **lpha-council** | LPHA | Core Council interface: updates risk equations, report formats |

All beacons are Low-Power (deterministic). Human governance provides all configuration. Monthly settlement cycle.

**Simplified:** No teleonome layer, no learning, no dreamer/actuator split, no probabilistic mesh.
**Preserved:** Beacon taxonomy, authority hierarchy, rate limits, audit trail, separation of concerns.

### Evolution Pathway

1. **Phase 1** — Teleonome-less beacons, monthly settlement, Synome-MVP
2. **Phases 2-8** — Daily settlement, LCTS, governed allocations, factory stack
3. **Phases 9-10** — Sentinel introduction (High-Power, AI, adaptive)
4. **Beyond roadmap** — Teleonome emergence, full probabilistic mesh, RSI at all levels

---

## 8. Structural Invariants

These must hold regardless of implementation or phase:

1. **Teleonomes think; beacons act** — Cognition is private; action is regulated
2. **Beacons are apertures, not minds** — They externalize intent, they don't have it
3. **All enforcement bottoms out in embodiments** — Physical infrastructure is the ultimate boundary
4. **Embodiments may host many agents and beacons** — Or none
5. **Beacon sophistication is constrained by embodiment power**
6. **Intelligence lives privately; power enters the world only through regulated apertures**

## 9. The Permanent Design Choices

1. Directives as universal human interface (Atlas, Agent Directive, Teleonome Directive -> Language Intent)
2. The core loop structure (Atlas -> Language Intent -> Axioms -> Library -> Language Intent)
3. The 5-layer model with clear containment
4. Content addressing (identity = hash of content)
5. The (strength, confidence) truth model on all assertions
6. Instantiation semantics (Axioms -> Sky; Library -> Teleonomes; Primitives -> Agent Types)
7. Alignment semantics (Voters -> Atlas; Council -> L1 components; Agent Types -> Teleonomes)
8. The primitive types (entity, relation, event, quantity, context, timestamp)
9. Language Intent as single translation layer
10. Probabilistic-deontic architecture

Everything else — storage backend, sync transport, query language — can be swapped.

---

## Document Index

| # | Document | Focus | Canonical For |
|---|----------|-------|---------------|
| 1 | `synome-overview.md` | Entry point: core idea, dual architecture | `dual-architecture` |
| 2 | `synome-layers.md` | Full 5-layer spec, components, artifacts, invariants | `five-layer-architecture`, `artifact-hierarchy`, `language-intent` |
| 3 | `atlas-synome-separation.md` | Atlas vs Synome, Mini-Atlases, governance window | `atlas-synome-separation`, `governance-window` |
| 4 | `synomic-agents.md` | Ledger-native entities, spectrum, autonomy, right to exist | — (referencing doc) |
| 5 | `beacon-framework.md` | Power x authority matrix, BEAMs, sentinels, naming | `beacon-framework` |
| 6 | `short-term-actuators.md` | Phase 1 beacons, evolution pathway | — (referencing doc) |
