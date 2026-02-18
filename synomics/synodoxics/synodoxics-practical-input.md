# Synodoxics: Practical Input

**Status:** Working notes from discussion. Input for formalizing the synodoxics axioms.

---

## What Synodoxics Is

Synodoxics is how you think as a shared brain, and how you individually think in a way that fully taps into the shared brain. Synlang is the convention for how you express synodoxics. The synodoxic mesh puts the meat on the macrosynomic skeleton, using governance gates (the crystallization interface) to transition from probabilities to deontics.

The core axioms should be few, simple, and implementation-invariant — they must carry over regardless of language, hardware, model, embodiment, or how much of the system has been rewritten. Well suited for constant vibe coding, self-rewrite, different hardware, different embodiments.

---

## The Five Axioms

### 1. The Mesh

The synodoxic mesh is probabilistic knowledge layered onto the macrosynomic skeleton. Unannotated knowledge is (1,1) — axiomatic, deontic. This means the skeleton IS the mesh: they are one system, where the skeleton is simply the subset with implicit (1,1) truth values. A plain knowledge graph with no truth value annotations is a valid Synome — everything in it is axiomatic. You soften parts of it by adding explicit truth values. The mesh grows outward from the skeleton as uncertainty gets expressed.

Governance gates (the crystallization interface) consume probabilistic evidence and produce deontic commitments. Once crystallized, decisions are clean and deterministic.

### 2. Truth Values

All probabilistic knowledge carries (strength, confidence) derived from positive and negative evidence weights. Strength is direction: is this good or bad? Confidence is weight: how much evidence? Ossification is arithmetic — high evidence resists change not because of policy, but because one new data point against a thousand existing ones barely moves the needle.

**The evidence-counting method is the core axiom.** Observe outcomes, update weights. This is described in the Atlas and is itself a synomic axiom at (1,1). It is the one epistemological commitment that is not subject to revision.

Other methods of setting truth values ("fudge methods") are permitted — oracle LLM assertions, governance overrides, arbitrary injection. But they earn their legitimacy through the evidence method. If a fudge method's outputs consistently converge with what the evidence method would eventually produce, that convergence pattern itself accumulates positive evidence, and the fudge method earns trust as a shortcut. If the fudge method's assertions keep getting contradicted by evidence, it loses credibility. Fudge methods are themselves patterns in the mesh, carrying their own truth values, earned through the canonical process.

At bootstrap this matters: there is no evidence yet, so you must fudge — LLMs seed beliefs, humans inject priors. Those seeded values are explicitly provisional, carrying whatever truth value the fudge method's (initially empty) track record justifies. The evidence process then validates or invalidates them.

### 3. Synlang as Thought

Synlang (s-expressions grounded in the synomic library and synome artifact) is the representation format for both the shared Synome AND individual telart. This is not just a communication protocol — it is a cognitive choice.

If you think in the same format the Synome stores knowledge, integration is zero-cost. Reading from the shared brain is just reading. Writing back is just writing. No translation layer. This is what "fully tapping into the shared brain" means concretely — native fluency in the Synome's language, not translation from an internal representation.

### 4. Neuro-Symbolic Architecture

The system is **symbolic-first**. It is a symbolic machine that uses neural nets as a subroutine, not an LLM with symbolic tools.

The execution flow is symbolic at the boundaries:

```
incoming data
    → symbolic system receives it
    → trivial structural checks (spam, malformed — reject without neural call)
    → call emo (embodied orchestrator) — because real reasoning requires neural heuristics
    → emo narrows the search space (fuzzy approximate pattern matching via neural weights)
    → symbolic system verifies and refines (precise probabilistic methods)
    → maybe calls emo again (loop)
    → loop resolves into a decision
    → if external action: symbolic checks gate the output
    → final irreversible call to beacon or hardware
```

The neural net never touches the outside world directly. It is sandboxed inside the symbolic loop. It thinks, it suggests, it pattern-matches — but every actual action passes through symbolic verification before it becomes real. The emo is powerful but contained. This is the cancer-logic defense made architectural.

**Why the neural net is almost always called:** brute-force probabilistic pattern matching across the mesh is computationally intractable at scale (subgraph isomorphism is NP-complete). The neural net's purpose from the symbolic perspective is to be a pattern matching query heuristic — it hallucinates approximate answers from its weights, narrowing the search space so the precise symbolic methods only have to verify and refine, not search from scratch. The only exception is trivially resolved inputs that fail basic structural checks before any reasoning is needed.

**The dual perspective:**
- From the LLM's perspective: the symbolic mesh exists to prepare optimal context for the neural net.
- From the symbolic perspective: the neural net exists to shortcut pattern matching that would be intractable to brute-force.

Same loop, two views. Cognition is the oscillation between fuzzy neural approximation and precise symbolic verification, where each side compensates for the other's weakness — speed vs rigor.

### 5. Context is Everything

From the LLM perspective, the entire challenge of thinking is context management. The synodoxic mesh, synart, telart, embart — all exist to give the embodied orchestrator and its subagents the best possible context in the shortest possible form. The mesh is not a database you query — it is a context preparation engine.

Teleonome economics makes this sharp: fixed-cost GPU, always spinning, so token throughput is the bottleneck. Every unnecessary token in context is time stolen from the next thought cycle. The "never idle" principle means the system's intelligence per unit time is directly proportional to how compressed and relevant its context is. A pattern that takes 200 tokens to express when 20 would do is literally making the system dumber — burning 10x the inference time for the same information.

Truth values serve context optimization directly. Strength and confidence are not just epistemic bookkeeping — they are relevance ranking signals for context assembly. High-confidence patterns deserve context space. Low-confidence patterns might not be worth loading. The ossification spectrum doubles as a priority system for what gets pulled into the next neural call.

Since the neural net is called on nearly every cycle, and its speed is bounded by context length, the symbolic system's most important job between neural calls is assembling the tightest possible context for the next call.

**Inverted (symbolic perspective):** pattern matching is everything. The neural net minimizes the amount of stuff that has to be searched probabilistically by hallucinating approximate results from its weights. Fuzzy neural weights produce candidates; precise probabilistic math verifies and refines; results reshape the next neural call. The tighter the context, the better the neural heuristic, the less the symbolic system has to search.

---

## Validation from Implementation

The RSI team's research_area_51 repo independently discovered several of these patterns:

- **Truth values:** The Othello RSI loop arrived at pos_weight/neg_weight with derived strength and confidence — the exact structure axiom 2 describes — without being told to. This suggests it's a natural attractor, not an arbitrary design choice.
- **Cancer-logic defense:** The team built a "Synome Police" that validates proposed strategies against immutable rules before they enter the store — the crystallization gate / symbolic check from axiom 4.
- **Neuro-symbolic loop:** Both RSI loops (Othello, Los Alamos) follow the pattern: LLM proposes (neural) → system validates (symbolic) → evidence accumulates (truth values) → context updates for next cycle.
- **The gap:** The working truth value system lives in a flat JSON strategy store, disconnected from the OWL/RDF knowledge graph. The formal knowledge representation (ontology.synlang) has no truth values. This is the sprint target — promoting the working pattern into the foundational layer.

---

## Relationship to Other Synodoxics Documents

This document provides practical grounding for the concepts described theoretically in:

- [`probabilistic-mesh.md`](probabilistic-mesh.md) — the mesh architecture, truth value model, RSI levels
- [`retrieval-policy.md`](retrieval-policy.md) — the five invariants for querying the mesh
- [`security-and-resources.md`](security-and-resources.md) — cancer-logic, ossification as defense, fractal security

The synlang research track ([`synlang-what-is.md`](synlang-what-is.md), [`synlang-hypergraph.md`](synlang-hypergraph.md), [`synlang-extensions.md`](synlang-extensions.md)) covers the formal language questions — notation, representation, scalability — that serve axiom 3.
