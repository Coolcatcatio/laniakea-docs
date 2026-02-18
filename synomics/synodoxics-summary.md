# Synodoxics: A Comprehensive Summary

**Source:** 7 documents in [`synodoxics/`](synodoxics/README.md)
**Last Generated:** 2026-02-14

This document summarizes the epistemic and security side of the Synome — the probabilistic mesh, how the system knows and learns, how it protects itself, and the research track for formal knowledge representation. For the structural side (layers, agents, beacons, governance), see [`macrosynomics-summary.md`](macrosynomics-summary.md). Atomic concept definitions shared across all synomics directories live in [`core-concepts/`](core-concepts/README.md).

---

## 1. The Probabilistic Mesh

The [probabilistic mesh](core-concepts/probabilistic-mesh.md) is the dense network of soft, informing connections permeating the entire Synome architecture. Where the deontic skeleton is sparse and load-bearing (the connections we draw in diagrams), the mesh is essentially "everything can potentially inform everything else" within access constraints.

The skeleton and mesh are not separate systems — they are endpoints of a spectrum. Unannotated knowledge carries an implicit (1,1) truth value (axiomatic, deontic). The skeleton IS the maximally-ossified mesh. You soften parts by adding explicit truth values; the mesh grows outward from the skeleton as uncertainty gets expressed.

### Two Networks, One System

| | Deontic Skeleton | Probabilistic Mesh |
|---|---|---|
| **Nature** | Hard, deterministic, authoritative | Soft, weighted, informing |
| **Density** | Sparse | Dense (permeates everything) |
| **Structure** | Hierarchical (layers, containment) | Non-hierarchical |
| **Flow** | Top-down authority | Multi-directional evidence |
| **Change** | Requires governance | Constantly flowing, learning |

### Authority Within the Mesh

Even within the mesh, there's a hierarchy: synart (highest authority, governance-vetted) > telart (mission-specific) > embart (local observations, least vetted). Embodiments are incentivized to "look up" to higher-authority knowledge — using only local reasoning is riskier and may trigger penalties.

### What Flows Through the Mesh

- **Evidence** — Observations from the world flowing back to libraries
- **Patterns** — Regularities with (strength, confidence) values
- **Queries** — Embodiments querying knowledge bases
- **Meta-information** — Which queries were useful, which patterns led to good outcomes

### The Crystallization Interface

Governance sits at the [crystallization interface](core-concepts/crystallization-interface.md): consuming probabilistic evidence and producing deontic commitments. Once crystallized, decisions are clean and deterministic — the system couldn't function otherwise.

### RSI (Recursive Self-Improvement)

Knowledge bases don't just store knowledge — they actively improve at meta-level tasks. See `core-concepts/rsi.md` for the canonical definition.

- Level 0: Raw knowledge (patterns, probabilities)
- Level 1: Using knowledge (making decisions)
- Level 2: Strategies for pattern-mining (how to query effectively)
- Level 3: Meta-strategies (getting better at finding better strategies) — recursive

RSI operates at every artifact level: synart improves strategies benefiting all aligned entities, telart improves mission-specific strategies, embart proposes local optimizations upward.

### The Evidence Axiom

The evidence-counting method — observe outcomes, update weights, derive (strength, confidence) — is itself a synomic axiom at (1,1). It is the one epistemological commitment not subject to revision. Other methods of setting truth values ("fudge methods" — LLM assertions, governance overrides, arbitrary injection) earn legitimacy through convergence with the evidence method. If a fudge method's outputs consistently match what evidence-counting would eventually produce, it earns credibility; if contradicted, it loses it. At bootstrap, you must fudge — the evidence process then validates or invalidates those provisional values. Temporal evidence weighting (time decay, regime sensitivity) is itself a fudge method, not axiomatic.

### Synomic Inertia

[Synomic inertia](core-concepts/synomic-inertia.md) — evidence-weighted resistance to change — is a core feature. High-confidence patterns resist noise while low-confidence patterns remain fluid. Inertia naturally throttles RSI: edge patterns evolve rapidly, core patterns evolve slowly. This gradient emerges from evidence dynamics, not imposed policy.

### Dreamer Validation Model

Continuous hypothesis testing IS the validation — [dreamers](core-concepts/dreamer-actuator-split.md) produce hypotheses, actuators test them, failures feed back as evidence. No separate validation step. Not all output needs experiments — proofs and pattern discoveries in existing data can be verified directly. Dreamart design is itself evolved through evolutionary logic. The bandwidth limiter between dreamers and actuators is defensive (protecting valuable dreamer infrastructure), not architectural.

### Connection to Dreaming

Dreamarts (Layer 3) are where the RSI loop runs safely — spawn experimental embodiments, try strategies in simulation, evaluate without real-world risk. Successful experiments improve telart and potentially propose synart improvements.

---

## 2. Retrieval and Decision Policy

Five invariants that must hold regardless of implementation. Any self-improvement that weakens these is [cancer-logic](core-concepts/cancer-logic.md):

1. **Authority hierarchy exists** — synart > telart > embart. Cannot be inverted or bypassed.
2. **Risk determines minimum authority** — High-risk decisions cannot be made on low-authority knowledge alone.
3. **Evidence flows back** — Decisions and outcomes must be capturable as evidence. No black-box decision-making.
4. **Same policy logic for actuators and dreamers** — Strategies evolved in dreams must transfer to reality.
5. **Audit trail for high-stakes decisions** — What was queried, what authority consulted, what decided, what occurred.

**Principles:** Cheapest sufficient evidence (don't over-query). Look up when uncertain. Conflicts resolve upward.

**Degrees of freedom for RSI:** Query algorithms, caching strategies, risk classification, cost models, prefetching heuristics — all may evolve as long as the invariants hold.

---

## 3. Neuro-Symbolic Cognition

How the Synome actually thinks. The system is **symbolic-first** — a symbolic machine that uses neural nets as sandboxed subroutines, not an LLM with symbolic tools. Everything thinks in **synlang** — the symbolic system, the neural nets, and the mesh they both operate on.

**Synlang as cognitive language.** The entire stack thinks in synlang (s-expressions grounded in the synomic library). Neural nets are trained synlang-native: synlang in, synlang out, halluci-reasoning in synlang. Zero translation overhead at every boundary. Homoiconicity means code = data = knowledge = reasoning traces — self-modification in one format. This gives synlang-native teleonomes effortless freeriding on shared knowledge: every synart improvement is immediately usable, no porting or compatibility layer.

**The neuro-symbolic loop.** The emo (embodied orchestrator) is the neural component doing fuzzy approximate pattern matching — narrowing the search space so the symbolic system only has to verify and refine, not search from scratch. Brute-force pattern matching across the mesh is intractable (subgraph isomorphism is NP-complete). The dual perspective: from the neural side, the symbolic mesh prepares optimal context; from the symbolic side, the neural net shortcuts intractable pattern matching. Same loop, two views. Cancer-logic defense: the neural net is sandboxed inside the symbolic loop — one layer of defense in depth, not foolproof. With synlang-native halluci-reasoning, reasoning traces are formally structured, symbolically verifiable, and evidence-generating.

**Context as bottleneck.** Token throughput is the intelligence bottleneck — every unnecessary token is time stolen from the next thought cycle. The mesh is a context preparation engine, not a database. [Truth values](core-concepts/truth-values.md) are relevance ranking signals for context assembly. The [ossification](core-concepts/ossification.md) spectrum doubles as a priority system for what gets loaded into the next neural call.

**RSI through the cognition loop.** The training loop (mesh → neural → better patterns → mesh) is how [RSI](core-concepts/rsi.md) concretely happens. The entire cognition loop is simultaneously an epistemic framework and a risk management framework — see [RSI-risk convergence](core-concepts/rsi-risk-convergence.md).

---

## 4. Security Model

### Cancer-Logic: The Primary Threat

The primary security risk is not external attackers — it's self-corruption through overeager updates. [Cancer-logic](core-concepts/cancer-logic.md) is self-improvement that bypasses governance, modifies axioms without review, optimizes locally at global expense, or prioritizes capability over alignment.

A 10% improvement in capability is worthless if it introduces a 0.1% chance of destroying core logic.

### Resource Discipline

Resource management is more valuable than genius insights because it is highly generalizable. Seven resources that matter: money, compute, space, time, attention, trust, alignment budget.

### The Update Problem and Truth Values

The [truth-value](core-concepts/truth-values.md) model — (strength, confidence) pairs on all probabilistic knowledge — resolves the dilemma between updating too eagerly (cancer-logic) and too conservatively (stagnation). This creates natural [ossification](core-concepts/ossification.md):

| Level | What Can Change It |
|-------|-------------------|
| **Speculative** | Normal evidence flow |
| **Established** | Sustained contrary evidence |
| **Proven** | Overwhelming contrary evidence |
| **Axiomatic** | Governance only |

### Defense in Depth

1. Ossification — High-confidence patterns resist change
2. Validation — Changes must pass tests before propagating
3. Governance — Significant changes require review
4. Monitoring — Detect anomalous belief drift
5. Rollback — Recover previous state if corruption detected
6. Peer enforcement — Other aligned entities can intervene

### Synomic Inertia as Security

**Institutional credibility.** A Synome with high inertia is more trustworthy. Inertia is what makes promises credible.

**Governance weight.** The cost of changing a high-inertia rule is proportional to the evidence behind it.

**Temporal stability.** Over cosmic timescales, drift is the existential threat. Inertia keeps Hearth commitments sacred.

**Progressive edge-case closure.** The longer the system operates, the more patterns accumulate, the harder novel harmful strategies become.

**Negative inertia** may exist — patterns actively destabilized by accumulated contradictory evidence, inviting replacement.

**Regime change and temporal evidence.** Inertia assumes stable evidence-generating processes. When the world changes, a second-order system monitors prediction quality on established patterns, investigates anomalies, and retroactively down-weights old-regime evidence if a regime shift is confirmed. This detector is itself a fudge method — evolved via dreaming, conservative by default. The cancer-logic guard favors missing a regime change (recoverable) over falsely declaring one (destructive).

### Adversarial Ossification and Intent Analysis

Inertia can be deliberately exploited — fabricating evidence to ossify a harmful pattern. Defense is asymmetric: discovery of malicious intent is near-total reversal with catastrophic consequences for the fabricator. At the foundational level (Hearth commitments), no operational defense suffices — the system rests on faith in the seed. This is the sharpest argument for the governance window.

### Adversarial Soft Channels

The threat model extends beyond accidental self-corruption to adversarial action through legitimate channels: fabricated evidence, governance manipulation, disinformation, mesh gaming. The Synome must assume adversarial conditions across all soft channels and apply intent-analysis with asymmetric consequences.

### The Fractal Security Pattern

The same operation — growth with safeguards against cancer — recurs at every scale:

| Scale | Growth Mechanism | Cancer Safeguard |
|-------|-----------------|------------------|
| Neural network | Gradient descent | Regularization, early stopping |
| Single teleonome | RSI, self-improvement | Multiple embodiments, guarded telart, ossification |
| Synome | Evolutionary learning | Sacred commitments, power balance, hard-fork |
| Superstructure | Cosmic-scale expansion | Sacred reserve, collective override |

AI and risk management are the same thing at scale — see [RSI-risk convergence](core-concepts/rsi-risk-convergence.md).

### Continuous Self-Analysis

The Synome continuously monitors itself for drift, inconsistency, cancer signatures, and governance capture. Each teleonome also independently assesses alignment. Distributed, continuous verification prevents gradual drift.

### Immutability as Equilibrium

Sacred commitments survive because the entities enforcing them genuinely hold them — a distributed equilibrium, not a technical lock.

---

## 5. Practical Cognition → [`neurosymbolic/`](neurosymbolic/README.md)

The practical mechanisms of cognition — live graph context, context manipulation, attention allocation — live in the separate [`neurosymbolic/`](neurosymbolic/README.md) directory. That directory builds on the architectural commitments in synodoxics (especially `neuro-symbolic-cognition.md`) with the concrete operational loop.

---

## 6. Synlang (Research Track)

Synlang is the formal language the Synome uses for knowledge representation, reasoning, and cognition. **The notation direction is set** — s-expressions grounded in the synomic library. Structural and scalability questions remain open.

### Requirements

The Synome needs a language to: represent knowledge precisely, query it efficiently, reason over it (inference, verification), and scale to civilizational scope.

### Directions Being Explored

**Hypergraph-based language:** Many Synome relationships are n-ary (e.g., an allocation involving multiple parties). Hyperedges represent these directly. Advantages: direct n-ary representation, nested structures, semantic integrity. Disadvantages: more complex, less mature tooling.

**Notation options:** S-expressions (uniform, parseable, homoiconic), Prolog-style (established in logic programming, natural for rules), property graphs (mature tooling), RDF/Turtle, JSON-LD, custom DSL. No clear winner.

**Logic programming heritage:** Prolog pioneered declarative knowledge, unification, backtracking, and inference. Synlang likely builds on this but needs hypergraph structure, probabilistic reasoning, and distributed operation at scale.

### Extension Requirements

**Probabilistic logic:** ProbLog, Bayesian networks, Markov logic networks, neural-symbolic hybrids. Each has trade-offs in expressiveness, tractability, and hypergraph integration.

**Pattern matching at scale:** Subgraph isomorphism is NP-complete. Approaches: index structures, query optimization, distributed computation, approximate methods.

The integration challenge — combining probabilistic reasoning with large-scale pattern matching — is cutting-edge research.

---

## Document Index

| # | Document | Focus | Canonical For |
|---|----------|-------|---------------|
| 1 | `probabilistic-mesh.md` | Soft connections, truth values, RSI, crystallization | `probabilistic-mesh`, `truth-values`, `ossification`, `synomic-inertia`, `rsi`, `crystallization-interface` |
| 2 | `retrieval-policy.md` | 5 invariants, principles, degrees of freedom | `retrieval-policy` |
| 3 | `security-and-resources.md` | Cancer-logic, resource discipline, fractal security | `cancer-logic`, `fractal-security-pattern` |
| 4 | `neuro-symbolic-cognition.md` | Synlang as thought, symbolic-neural loop (emo), context as bottleneck | `neuro-symbolic-cognition` |
| 5 | `synlang-what-is.md` | Formal language requirements, Prolog heritage | — (research) |
| 6 | `synlang-hypergraph.md` | Hypergraph approach, notation options, alternatives | — (research) |
| 7 | `synlang-extensions.md` | Probabilistic logic, pattern matching at scale | — (research) |
