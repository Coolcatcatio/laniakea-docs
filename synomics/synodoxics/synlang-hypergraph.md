---
concepts:
  references: []
---

# Exploring Hypergraph Structure

One promising direction for synlang is a hypergraph-based approach. This document explores that option — its potential advantages, open questions, and alternatives.

**This is exploratory, not prescriptive.**

---

## The Hypergraph Idea

### What's a Hypergraph?

A hypergraph generalizes graphs:
- **Standard graph**: Edges connect exactly two nodes
- **Hypergraph**: Edges (hyperedges) connect any number of nodes

### Why Consider This?

Many relationships involve more than two things:

**Example: An allocation**
"Generator allocates 40% to Spark, 30% to Grove, 30% to Keel"

This is ONE decision involving multiple parties. In a standard graph, you'd decompose it into many separate edges, losing the unity.

A hyperedge can represent it as one structure:
```
(allocation Generator
  (to Spark 40)
  (to Grove 30)
  (to Keel 30))
```

### Potential Advantages

- Direct representation of n-ary relationships
- Nested structures are natural
- Pattern matching over complex structures
- Semantic integrity (related facts stay together)

### Potential Disadvantages

- More complex than simple graphs
- Less mature tooling
- Harder to implement efficiently
- May be overkill for simple cases

---

## The Bracket Notation Option

### S-Expressions

One option for synlang syntax is s-expressions (Lisp-style brackets):

```
(operator arg1 arg2 arg3 ...)
```

**Potential advantages:**
- Uniform structure
- Trivial to parse
- Homoiconicity (code = data)
- Macros and self-extension

**Potential disadvantages:**
- Unfamiliar to many
- Can become deeply nested
- Not obviously the best choice

### Prolog-Style

Another option draws from Prolog's syntax:

```prolog
allocation(generator, [(spark, 40), (grove, 30), (keel, 30)]).
is_prime(spark).
controls(sky, generator).
```

**Potential advantages:**
- Established in logic programming
- Natural for rules and queries
- Good for inference

**Potential disadvantages:**
- Binary predicates don't naturally express hyperedges
- May need extensions for complex structures

### Other Alternatives

**Property graph style:**
```
(Spark)-[:ALLOCATION {amount: 40}]->(Generator)
```

**RDF/Turtle style:**
```
:Spark :allocatedFrom :Generator ;
       :amount 40 .
```

**JSON-LD style:**
```json
{
  "@type": "Allocation",
  "from": "Generator",
  "to": [{"agent": "Spark", "percent": 40}]
}
```

**Custom DSL:**
Something entirely new optimized for Synome use cases.

**The right choice is unclear.** Each has trade-offs.

---

## Comparison with Alternatives

### Property Graphs (Neo4j-style)

Property graphs have nodes and edges with key-value properties.

**Advantages:**
- Mature tooling (Neo4j, etc.)
- Well-understood query languages (Cypher)
- Good performance characteristics

**Limitations:**
- Fundamentally binary edges
- Properties are flat, not structured
- Hyperedges require workarounds

**Question:** Can property graphs be extended, or do we need hypergraphs?

### RDF/OWL

RDF represents knowledge as subject-predicate-object triples.

**Advantages:**
- Mature standard with tooling
- Rich semantics (OWL)
- Interoperability

**Limitations:**
- Triple model requires reification for complex relationships
- Can be verbose
- Performance at scale can be challenging

**Question:** Is RDF's approach adaptable, or fundamentally limited?

### Datalog

Datalog is a declarative logic programming language (related to Prolog).

**Advantages:**
- Well-understood semantics
- Efficient evaluation algorithms
- Good for recursive queries

**Limitations:**
- Typically binary/ternary predicates
- Less expressive than full Prolog
- May need extensions for hypergraphs

**Question:** Can Datalog be extended for hypergraph structures?

### Relational (SQL)

Relational tables can model n-ary relationships.

**Advantages:**
- Extremely mature
- Well-understood optimization
- Scales well for many use cases

**Limitations:**
- Fixed schema
- No native nesting
- Weak pattern matching
- Impedance mismatch with graph structures

**Question:** Could a relational approach work with the right abstractions?

---

## Open Questions

### Is Hypergraph the Right Model?

Maybe. But consider:
- Do we need full hyperedge generality?
- Could a simpler model work with conventions?
- What's the implementation cost?

### What Notation?

Even if hypergraph is right, the syntax is open:
- S-expressions?
- Prolog-style?
- Something more familiar?
- Multiple syntaxes for different contexts?

### How to Handle Types?

Type systems have trade-offs:
- **Strong types**: Catch errors early, more verbose
- **Weak/dynamic types**: Flexible, errors at runtime
- **Gradual typing**: Hybrid approach

What fits synlang best?

### Schema vs Schemaless?

- **Schema**: Structure enforced, evolution harder
- **Schemaless**: Flexible, consistency challenges
- **Schema-optional**: Best of both? Or worst?

---

## Research Directions

If pursuing the hypergraph approach:

**Theoretical:**
- Formal semantics for hypergraph synlang
- Complexity of query operations
- Type theory for hypergraphs

**Practical:**
- Prototype implementations
- Performance benchmarks
- Comparison with alternatives

**Implementation:**
- Storage formats
- Indexing strategies
- Distributed operation

---

## What Would Help

To evaluate the hypergraph approach:

1. **Prototypes** — Build small implementations, see what works
2. **Benchmarks** — Test performance on realistic data
3. **Use cases** — Map Synome needs to representation requirements
4. **Comparisons** — Try the same problem in different approaches
5. **Expert input** — Learn from graph database and logic programming communities

---

## Summary

1. Hypergraphs are ONE promising direction for synlang
2. They offer direct representation of complex relationships
3. Multiple notation options: s-expressions, Prolog-style, others
4. Alternatives exist: property graphs, RDF, Datalog, relational, custom
5. Many questions are open: model, syntax, types, schema
6. This needs exploration, prototyping, and comparison
7. No decision has been made — we're exploring the space

