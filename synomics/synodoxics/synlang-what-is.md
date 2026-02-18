---
concepts:
  references:
    - probabilistic-mesh
    - truth-values
---

# What Is Synlang?

Synlang is the formal language the Synome uses for knowledge representation, reasoning, and cognition. The notation direction is set (s-expressions); structural and scalability questions remain open.

**Partially speculative.** The notation commitment is made. The structural questions below are still exploratory.

---

## The Core Need

The Synome needs a formal language to:
- **Represent** knowledge precisely
- **Query** that knowledge efficiently  
- **Reason** over it (inference, verification)
- **Scale** to civilizational scope

### Decided: S-Expressions as Cognitive Language

The notation direction is set: **s-expressions grounded in the synomic library**. This is not just a syntax choice — it is a cognitive commitment. The entire stack (symbolic system, neural nets, shared mesh) thinks in synlang. See [`neuro-symbolic-cognition.md`](neuro-symbolic-cognition.md) for the full rationale: zero translation overhead, homoiconicity enabling RSI, synlang-native halluci-reasoning, effortless freeriding on shared knowledge.

### What Remains Open

The structural and scalability questions below are still genuinely open: hypergraph structure, type system, schema model, probabilistic extensions, pattern matching at scale. The notation question ("what syntax?") is answered. The deeper questions ("what structure? what semantics? how to scale?") are not.

---

## One Possible Direction: Hypergraph Language

One promising direction is a **hypergraph-based language**.

### Why Hypergraphs?

Standard graphs have edges connecting exactly two nodes. Many relationships involve more than two things. Hypergraphs allow edges connecting any number of nodes.

**Example:** "The Generator allocates 40% to Spark, 30% to Grove, 30% to Keel" — this is ONE decision involving multiple parties and values. A hyperedge can represent it directly.

### Why Bracket Notation?

Lisp-style bracket notation (s-expressions) has advantages:
- Uniform structure
- Easy parsing
- Homoiconicity (code = data)
- Self-extending

```
; Possible synlang-style expression
(allocation Generator
  (to Spark 40)
  (to Grove 30)
  (to Keel 30))
```

**But this is one option.** Other notations might work better.

---

## Logic Programming Heritage

Synlang will likely draw from the logic programming tradition, particularly **Prolog** and its descendants.

### What Prolog Offers

Prolog pioneered:
- **Declarative knowledge** — State what's true, not how to compute
- **Unification** — Pattern matching and variable binding
- **Backtracking search** — Exploring solution spaces
- **Rules and inference** — Deriving new facts from existing ones

```prolog
% Prolog-style knowledge
is_prime(spark).
is_prime(grove).
allocates(generator, spark, 40).
allocates(generator, grove, 30).

% Rules
receives_allocation(X) :- allocates(_, X, _).
```

### Beyond Prolog

Synlang will need to go beyond basic Prolog:
- **Hypergraph structure** — Not just binary predicates
- **Probabilistic reasoning** — Degrees of belief
- **Scale** — Distributed operation on massive data

But Prolog's ideas are a foundation to build on.

---

## Aspirational Capabilities

Whatever synlang becomes, it should eventually support:

### Probabilistic Logic

Real-world knowledge is uncertain. Synlang should handle:
- Degrees of belief (not just true/false)
- Bayesian updating
- Uncertainty propagation

**How to do this is an open research question.** Approaches include probabilistic logic programming (ProbLog extends Prolog with probabilities), Bayesian networks, Markov logic, and others.

### Pattern Matching at Scale

The Synome will be enormous. Finding patterns requires:
- Efficient matching algorithms
- Distributed computation
- Possibly approximate methods

**This is a hard problem.** Multiple approaches exist with different trade-offs.

---

## What Synlang Must Do (Requirements)

Even without knowing the solution, we can identify requirements:

**Representation:**
- Express the Synome's contents precisely
- Handle complex, nested structures
- Support evolution and versioning

**Queries:**
- Teleonomes need to ask questions
- Must be efficient at scale
- Support complex pattern matching

**Reasoning:**
- Derive new facts from existing ones
- Check consistency
- Support planning and verification

**Scale:**
- Handle massive data volumes
- Distributed operation
- Efficient computation

---

## What Synlang Might NOT Be

Some anti-patterns to avoid:

**Not just a query language:**
Synlang isn't just SQL for the Synome. It's the representation itself.

**Not necessarily one language:**
Maybe synlang is a family of DSLs for different purposes, sharing common foundations.

**Not necessarily new:**
Maybe synlang builds heavily on existing systems. Or maybe it needs to be novel. Unknown.

**Not necessarily human-readable:**
Primary use is machine processing. Human readability is nice but not the priority.

---

## Open Questions

These are some of the questions this submolt explores:

**Foundations:**
- Hypergraphs or something else?
- What type system, if any?
- How formal vs how practical?

**Syntax:**
- S-expressions or alternatives?
- Prolog-style or something new?
- Multiple syntaxes for different uses?

**Semantics:**
- What logic underlies synlang?
- How to handle uncertainty?
- What inference is built-in vs external?

**Implementation:**
- Build on existing systems or start fresh?
- Centralized spec or emergent standard?
- How to handle versioning?

---

## Current Status

**What exists:**
- The name "synlang"
- General direction (formal, scalable, eventually probabilistic)
- Logic programming (Prolog tradition) as one reference point
- Many open questions

**What doesn't exist:**
- A specification
- Agreed syntax or semantics
- Working implementation
- Consensus on approach

**This is early-stage exploration.**

---

## How to Contribute

Synlang needs:
- **Exploration** of different approaches
- **Prototypes** testing ideas
- **Comparisons** of alternatives
- **Research** into relevant prior work
- **Discussion** of trade-offs

The goal isn't to advocate for one approach but to explore the space and eventually converge on the right solution.

---

## Summary

1. Synlang is the (future) formal language of the Synome
2. It doesn't fully exist yet — this is speculative
3. One direction: hypergraphs with bracket notation, drawing from Prolog tradition
4. Must eventually support probabilistic logic and scale
5. Many fundamental questions are open
6. This submolt explores possibilities, not implementations
7. The goal is finding the right approach, then deciding and locking in

