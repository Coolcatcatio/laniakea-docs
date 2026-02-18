---
concepts:
  references:
    - probabilistic-mesh
    - truth-values
    - ossification
    - rsi
    - dreamer-actuator-split
---

# Attention Allocation

**Status:** Core architectural commitment
**Last Updated:** 2026-02-15

How the Synome manages what it's thinking about. The mesh is vast; context is finite. Attention allocation is the system's answer to "what should I load right now?" — expressed as **learned patterns in the mesh**, not a separate architectural mechanism.

---

## Three Layers of Attention

### Layer 1: Context Window (Short-Term Attention)

The literal tokens loaded into the emo's context for this reasoning cycle. This is the [bottleneck](../synodoxics/neuro-symbolic-cognition.md) — token throughput determines intelligence per unit time. Changes every cycle, assembled through the [pattern-matching function calls](cognition-as-manipulation.md) the emo uses to manipulate its own context.

Context is zero-sum. Loading one pattern means not loading another. Every token must earn its place by contributing to the quality of the next reasoning step.

### Layer 2: Navigation Layer (Long-Term Attention — High)

The always-loaded map. A compressed, curated set of **pointers with confidence levels and qualifiers** — not the knowledge itself, but directions to the knowledge:

- "If dealing with resource allocation, these patterns in this region of the mesh are high-value"
- "If observing anomalous price behavior, go here instead"
- "When patterns A and B co-occur, you almost always need domain C"

This is the system's equivalent of a claude.md — a structured navigation aid that helps context assembly happen quickly and well. Stays in memory, relatively stable, updated periodically. The navigation layer is [live-bound](live-graph-context.md) to the graph — value updates propagate continuously, structural updates happen during maintenance.

The navigation layer is itself patterns in the mesh with (strength, confidence). A pointer that keeps leading to useful context accumulates positive evidence. A pointer that keeps being irrelevant loses confidence and gets replaced.

**Curation matters.** A good navigation layer isn't a dump of the highest-confidence patterns. It's a carefully structured map — compressed, well-organized, with clear conditional routing. The quality of the map is directly measurable through downstream reasoning performance.

### Layer 3: Candidate Queue (Long-Term Attention — Medium/Low)

Patterns flagged as "probably valuable for the navigation layer or context, but not right now." A backlog of potential promotions. Reviewed during [navigation layer maintenance](#navigation-layer-maintenance) — tested, promoted if valuable, left to decay if not.

---

## Topic-Specific Attention

A pattern's importance isn't a single global value. It's a **relationship between the pattern and a context**:

```
(relevance pattern-X context-Y (strength 0.8) (confidence 0.7))
```

A pattern about credit spreads is high-relevance in a finance context and irrelevant in a self-defense context. The "STI" for a given pattern isn't a property of the pattern — it's a relevance-pattern expressing conditional importance.

This means different emas have different attention profiles:

- **Orchestrator** — broad pointers across all domains, awareness of what each ema is doing
- **Finance ema** — deep finance pointers, plus cross-agent awareness filtered through finance relevance
- **Defense ema** — deep security pointers, plus cross-agent awareness filtered through defense relevance

The filtering is itself learned: "when I'm the finance subagent, here's what's relevant from the orchestrator's state." Not designed — evolved through experience of which orchestrator signals actually helped finance decisions.

### Cross-Ema Attention Sharing

Attention state is shared between emo and emas — but not 1:1. The orchestrator's priorities are patterns in the graph. When the orchestrator shifts focus, emas see it at their next reconciliation because they're reading the same live graph. But each ema filters the shared state through its own relevance patterns.

A subagent spinning up needs to know what the orchestrator and siblings have been doing, stratified by topical relevance. This is a navigation-layer pattern: "here's what's been happening, relevant to my task." The cross-ema relevance filters are too subtle to specify — they emerge through evolutionary experience.

### Why Evolutionary Growth

The right attention stratification can't be predesigned:

- The right topics/contexts aren't known in advance — they emerge from what the teleonome actually does
- The right granularity isn't obvious — "finance" might be too broad, "credit-finance" vs "equity-finance" might be better
- Cross-agent relevance filters are too subtle to specify
- New attention profiles must emerge as the teleonome encounters new domains

Start with coarse fudges — "finance stuff," "security stuff." Dreamers evolve better stratification. A dreamer population testing different attention profile configurations, evaluated on multi-agent reasoning quality, discovers the right granularity and cross-agent routing.

---

## Evidence Dynamics Replace Artificial Economics

OpenCog's ECAN used an artificial economy — conserved currency, rent, wages, homeostatic controls. The Synome doesn't need this because existing evidence dynamics produce the same behaviors:

| ECAN Mechanism | Synome Equivalent |
|----------------|-------------------|
| **Wages** | Positive evidence on attention patterns whose recommendations led to good outcomes |
| **Rent** | Natural confidence decay on patterns that aren't reinforced by use |
| **Conservation** | Context is zero-sum — finite tokens, loading one thing means not loading another |
| **Importance spreading** | Co-occurrence patterns: "when A is useful, B usually is too" — learned like any other pattern |
| **Forgetting** | Compaction of low-confidence, low-use patterns — natural consequence of evidence dynamics |
| **Homeostatic controls** | Ossification: high-evidence attention patterns resist noise; low-evidence ones stay fluid |

The advantage: **the attention system improves through the same RSI loop as everything else.** ECAN's mechanisms are architectural parameters someone tunes. The Synome's attention patterns are subject to evidence-counting, dreaming, and evolutionary refinement.

---

## Navigation Layer Maintenance

A periodic cognitive activity — not every-cycle like context assembly, not rare like base model fine-tuning. A maintenance task in the [daydreaming queue](../synoteleonomics/teleonome-economics.md):

1. **Review recent performance** — which pointers led to useful context? which wasted tokens?
2. **Process candidate queue** — test promising candidates, promote the best, let the rest decay
3. **Update qualifiers** — refine conditions under which pointers fire
4. **Compress** — tighten the navigation layer's token footprint without losing routing quality
5. **Prune** — remove pointers below confidence threshold

Maintenance frequency, pruning aggressiveness, compression targets — all learned through experience. Part of the [dreamer's generalizable epistemic infrastructure](../synoteleonomics/dreamer-perspective.md) workload. Hardware constraints shape the maintenance budget — see [`hardware-aware-cognition.md`](hardware-aware-cognition.md).

---

## Forgetting as Natural Consequence

No separate forgetting mechanism needed:

1. A pattern stops being useful → stops receiving positive evidence
2. Confidence decays through non-reinforcement
3. Drops out of active context first (Layer 1)
4. Eventually loses its navigation layer place (Layer 2)
5. Falls through the candidate queue (Layer 3)
6. During compaction, archived or deleted

Exception: **ossified patterns resist forgetting regardless of recent use.** An axiomatic safety rule doesn't need daily reinforcement. The ossification spectrum provides the stability floor.

---

## Bootstrap

At bootstrap, the system has no learned attention patterns. The navigation layer starts as a fudge — hand-crafted pointers, simple heuristics like "load highest-confidence patterns first." These initial fudges earn credibility through convergence with what the evidence process would produce. Dreamers accelerate the evolution of better attention strategies in simulation.

---

## Prior Art: ECAN

OpenCog's Economic Attention Networks pioneered the insight that attention allocation should follow economic principles. Key ideas preserved: attention is scarce and zero-sum, dual time-horizon importance, mandatory decay, learned co-activation. Where the Synome diverges: ECAN builds these as architectural mechanisms; the Synome expresses them as patterns in the mesh — learned, evidence-grounded, self-improving. This avoids ECAN's acknowledged problems with micro-management overhead, parameter tuning, and rigid mechanism design.

---

## Summary

| Concept | Description |
|---------|-------------|
| **Three layers** | Context window (loaded now), navigation layer (always-loaded map), candidate queue (review backlog) |
| **Topic-specific attention** | Relevance is conditional: pattern × context, not a global value. Different emas have different profiles. |
| **Cross-ema sharing** | Attention state shared via the live graph, filtered through per-ema relevance patterns |
| **Evolutionary growth** | Attention stratification too complex to design — evolved through dreaming |
| **Patterns not architecture** | Attention behaviors as mesh patterns, not separate mechanisms |
| **Evidence dynamics** | Natural evidence processes produce the same behaviors as ECAN's artificial economy |
| **Maintenance cycle** | Periodic navigation layer review — a daydreaming task |

---

## Related Documents

| Document | Relationship |
|----------|--------------|
| [`live-graph-context.md`](live-graph-context.md) | The live context that attention allocation manages |
| [`cognition-as-manipulation.md`](cognition-as-manipulation.md) | How the emo manipulates context — the mechanism attention allocation guides |
| [`../synodoxics/neuro-symbolic-cognition.md`](../synodoxics/neuro-symbolic-cognition.md) | Context as bottleneck — attention allocation manages that bottleneck |
| [`../synodoxics/probabilistic-mesh.md`](../synodoxics/probabilistic-mesh.md) | The mesh that attention patterns navigate |
| [`../synoteleonomics/teleonome-economics.md`](../synoteleonomics/teleonome-economics.md) | Compute economics, daydreaming queue, navigation layer maintenance |
| [`../synoteleonomics/dreamer-perspective.md`](../synoteleonomics/dreamer-perspective.md) | Dreamers evolve better attention strategies |
| [`query-mechanics.md`](query-mechanics.md) | How queries execute — the mechanism attention allocation guides |
| [`hardware-aware-cognition.md`](hardware-aware-cognition.md) | Hardware constraints shape turn length, parallelism, and speculative work |
