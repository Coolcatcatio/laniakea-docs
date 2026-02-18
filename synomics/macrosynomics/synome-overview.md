---
concepts:
  defines:
    - dual-architecture
  references:
    - five-layer-architecture
    - artifact-hierarchy
    - beacon-framework
    - probabilistic-mesh
    - truth-values
    - ossification
    - synomic-inertia
    - rsi
    - cancer-logic
    - language-intent
    - atlas-synome-separation
    - retrieval-policy
    - crystallization-interface
    - dreamer-actuator-split
---

# Synome Architecture Overview

**Status:** Conceptual foundation
**Last Updated:** 2026-02-12

This document provides a high-level introduction to the Synome architecture and the canonical treatment of the dual architecture. Each concept is detailed in companion documents.

---

## The Core Idea

> **Intelligence lives privately; power enters the world only through regulated apertures.**

The Synome architecture separates **private cognition** from **public action**. Teleonomes (autonomous AI systems) think privately, but can only affect the world through regulated, observable, revocable interfaces called [beacons](../core-concepts/beacon-framework.md).

This separation enables AI systems to be both capable and constrained — they can improve themselves, accumulate knowledge, and pursue goals, but their power to act is always governed.

---

## Architecture at a Glance

The [five-layer architecture](../core-concepts/five-layer-architecture.md) moves from shared public truth (Layers 1-2) down to private execution (Layers 3-5). See [`synome-layers.md`](synome-layers.md) for full specifications.

Knowledge accumulates in the [artifact hierarchy](../core-concepts/artifact-hierarchy.md): synart (shared) > telart (per teleonome) > embart (per embodiment). Higher-level knowledge is more authoritative.

[Beacons](../core-concepts/beacon-framework.md) — classified by power × authority into LPLA/LPHA/HPLA/HPHA — are the regulated apertures through which teleonomes affect the world. See [`beacon-framework.md`](beacon-framework.md) for the full taxonomy.

[Synomic Agents](atlas-synome-separation.md) (Primes, Halos, Generator, Guardian) are durable, ledger-native entities constrained by publicly visible rules — credible commitment devices enabling cooperation without trust.

Embodiments come as [actuators and dreamers](../core-concepts/dreamer-actuator-split.md) — actuators execute in reality, dreamers explore in simulation. Successful patterns flow from dreamers to actuators via telart.

All directives pass through [Language Intent](../core-concepts/language-intent.md) — a single trusted translation layer grounded by the Synomic Library. See [`synome-layers.md`](synome-layers.md) for the human interface pattern.

---

## The Dual Architecture

The Synome has two fundamentally different types of structure operating simultaneously:

### Deontic Skeleton (Hard)

The **deontic skeleton** is sparse, load-bearing, and deterministic:
- Axioms that must be followed
- Governance decisions
- Authority hierarchies
- Enforcement mechanisms

Once a rule is set, it holds unconditionally. These are the connections we draw in architecture diagrams — the hard links between layers.

### Probabilistic Mesh (Soft)

The [probabilistic mesh](../core-concepts/probabilistic-mesh.md) is dense, informing, and adaptive — essentially "everything can potentially inform everything else" within access constraints. It carries knowledge with [truth values](../core-concepts/truth-values.md) (strength, confidence), evidence flowing back from the world, patterns that [ossify](../core-concepts/ossification.md) over time, and [RSI](../core-concepts/rsi.md). It informs decisions; the skeleton executes them. See [`probabilistic-mesh.md`](../synodoxics/probabilistic-mesh.md) for the full model.

### The Crystallization Dynamic

Governance sits at the [crystallization interface](../core-concepts/crystallization-interface.md) — consuming probabilistic evidence, producing deontic commitments:

```
Evidence accumulates    →    Governance deliberates
Patterns emerge         →    Decision is made
(strength, confidence)  →    Rule is set (1,1)
Knowledge mesh informs  →    Skeleton executes
```

The system can't function if everything is probabilistic — you need a skeleton of deontic connections (sparse, load-bearing) surrounded by a mesh of probabilistic connections (dense, informing). Once a decision is made, it crystallizes into a clean deontic rule.

---

## Security

The primary security threat is [cancer-logic](../core-concepts/cancer-logic.md) — self-corruption through overeager updates, not external attackers. [Synomic inertia](../core-concepts/synomic-inertia.md) — evidence-weighted resistance to change — protects high-confidence patterns while allowing edge patterns to evolve rapidly. See [`security-and-resources.md`](../synodoxics/security-and-resources.md) for the full security model.

---

## Implementation Pathways

The full architecture is built incrementally:

| Pathway | Focus | Detailed In |
|---------|-------|-------------|
| **Short-term actuators** | Teleonome-less beacons (Phase 1) | [`short-term-actuators.md`](short-term-actuators.md) |
| **Short-term dreamers** | Game-playing agents | [`short-term-experiments.md`](../neurosymbolic/short-term-experiments.md) |

Both pathways preserve the essential invariants while simplifying for practical deployment.

---

## Document Map

### Macrosynomics (Structure)

| Document | What It Covers |
|----------|----------------|
| [`synome-layers.md`](synome-layers.md) | 5-layer architecture, components, artifact hierarchy, structural invariants |
| [`atlas-synome-separation.md`](atlas-synome-separation.md) | Atlas vs Synome, Synomic Agent autonomy, governance window, verification model |
| [`beacon-framework.md`](beacon-framework.md) | Beacon taxonomy, lifecycle, naming, sentinel formations |
| [`synomic-agents.md`](synomic-agents.md) | Synomic Agents as ledger-native entities — spectrum, lifecycle, power through integration |
| [`short-term-actuators.md`](short-term-actuators.md) | Phase 1 beacon implementation pathway |

### Synodoxics (Knowledge and Security)

| Document | What It Covers |
|----------|----------------|
| [`probabilistic-mesh.md`](../synodoxics/probabilistic-mesh.md) | Truth values, ossification, evidence flow, RSI, crystallization |
| [`retrieval-policy.md`](../synodoxics/retrieval-policy.md) | Query invariants for the probabilistic mesh |
| [`security-and-resources.md`](../synodoxics/security-and-resources.md) | Cancer-logic, resource discipline, synomic inertia, fractal security |
| [`synlang-what-is.md`](../synodoxics/synlang-what-is.md) | Formal language for the Synome — requirements, Prolog heritage |
| [`synlang-hypergraph.md`](../synodoxics/synlang-hypergraph.md) | Hypergraph-based approach, s-expressions, alternatives |
| [`synlang-extensions.md`](../synodoxics/synlang-extensions.md) | Probabilistic logic and pattern matching at scale |

### See Also

| Directory | What It Covers |
|-----------|----------------|
| [`../synoteleonomics/`](../synoteleonomics/README.md) | Teleonome study — design, economics, memory, resilience, binding, purpose, game theory |
| [`../core-concepts/`](../core-concepts/README.md) | Atomic concept definitions shared across all narrative directories |

---

## One-Line Summary

> *The Synome architecture separates private cognition from public action: teleonomes accumulate knowledge and pursue goals privately, but power enters the world only through regulated beacons, governed by the Synome, with security achieved through synomic inertia rather than restriction.*
