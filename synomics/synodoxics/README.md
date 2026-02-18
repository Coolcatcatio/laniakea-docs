# Synodoxics

The study of synomic belief, knowledge dynamics, and how the Synome knows and learns. From *doxa* (belief) — synodoxics covers the probabilistic mesh, the security model that protects it, the retrieval policies that govern access to it, and the formal language research for representing it.

Where [macrosynomics](../macrosynomics/README.md) defines what the Synome IS (structure, entities, authority, governance), synodoxics defines what the Synome BELIEVES and how it learns — the epistemic and security half of the dual architecture.

---

## Reading Order

### Core Epistemic Architecture

Start here for the knowledge dynamics.

1. **[`probabilistic-mesh.md`](probabilistic-mesh.md)** — The dense network of soft, informing connections overlaid on the deontic skeleton. Truth values (strength, confidence), crystallization interface, RSI levels, synomic inertia. The foundational document for synodoxics.
2. **[`retrieval-policy.md`](retrieval-policy.md)** — Invariants, principles, and degrees of freedom for querying the probabilistic mesh. Risk-gates-authority constraint.
3. **[`security-and-resources.md`](security-and-resources.md)** — Cancer-logic as the primary threat. Ossification as the solution. Resource discipline, defense in depth, the update problem, adversarial soft channels, fractal security pattern.

### Cognition

How the Synome actually thinks — the architectural commitments. For the practical mechanisms (live context, context manipulation, attention), see [`../neurosymbolic/`](../neurosymbolic/README.md).

4. **[`neuro-symbolic-cognition.md`](neuro-symbolic-cognition.md)** — Synlang as cognitive language, the symbolic-neural loop (emo), context as bottleneck. How the mesh gets used.

### Research: Synlang

The notation direction is set (s-expressions grounded in the synomic library). Structural and scalability questions remain open.

5. **[`synlang-what-is.md`](synlang-what-is.md)** — Requirements, Prolog heritage, remaining open questions.
6. **[`synlang-hypergraph.md`](synlang-hypergraph.md)** — Hypergraph-based approach, comparison with alternatives.
7. **[`synlang-extensions.md`](synlang-extensions.md)** — Probabilistic logic and pattern matching at scale.

### Working Notes

8. **[`synodoxics-practical-input.md`](synodoxics-practical-input.md)** — Working notes from discussion — input for formalizing the synodoxics axioms.
9. **[`synodoxics-todo.md`](synodoxics-todo.md)** — Open items: terminology consistency ("synomic inertia" vs "ossification"), cross-boundary reference maintenance.

---

## Dependency Graph

```
probabilistic-mesh
    ├──► retrieval-policy
    ├──► security-and-resources
    └──► neuro-symbolic-cognition ──► ../neurosymbolic/ (practical mechanisms)

synlang-what-is ──► synlang-hypergraph ──► synlang-extensions
(independent research track)
```

The core epistemic track and the synlang research track are independent — synlang is about formal representation of the concepts described in the epistemic docs, but doesn't depend on reading them first.

---

## Relationship to Siblings

**[Macrosynomics](../macrosynomics/README.md)** defines structure: layers, agents, beacons, governance. Synodoxics defines knowledge: what the Synome believes, how it learns, how it protects itself. The two are the structural (deontic) and epistemic (probabilistic) halves of the [dual architecture](../core-concepts/dual-architecture.md).

**[Synoteleonomics](../synoteleonomics/README.md)** describes the entities that inhabit the structures macrosynomics defines and use the knowledge synodoxics governs. Teleonome memory, resilience, and economics all depend heavily on the probabilistic mesh and security model.

**[The Hearth](../hearth/README.md)** provides the purpose — the Hearth commitments that the security model ultimately protects.

Atomic concept definitions shared across all directories live in [`../core-concepts/`](../core-concepts/README.md).
