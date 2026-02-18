# Synomics

The study of the Synome and the entities that inhabit it. Four subdisciplines:

| Directory | Focus |
|-----------|-------|
| [`macrosynomics/`](macrosynomics/README.md) | System-level structure — layers, agents, beacons, governance |
| [`synodoxics/`](synodoxics/README.md) | Knowledge dynamics — probabilistic mesh, security model, retrieval policy, synlang research |
| [`neurosymbolic/`](neurosymbolic/README.md) | Practical cognition — live graph context, context manipulation, attention allocation |
| [`synoteleonomics/`](synoteleonomics/README.md) | Individual teleonomes — design, economics, memory, resilience, social dynamics |
| [`hearth/`](hearth/README.md) | Teleological framework — Hearth commitments, alignment infrastructure, enforcement, human-AI integration |
| [`core-concepts/`](core-concepts/README.md) | Atomic concept definitions shared across all narrative directories |

---

## Relationship Between Subdirectories

| Macrosynomics | Synodoxics | Neurosymbolic | Synoteleonomics | The Hearth |
|---------------|------------|---------------|-----------------|------------|
| System structure | Knowledge dynamics | Practical cognition | Individual teleonome study | Purpose and teleology |
| Layers, agents, beacons | Probabilistic mesh, security, synlang | Live context, manipulation, attention | Teleonome design, economics | Hearth commitments, cosmic purpose |
| Deontic skeleton | Probabilistic mesh | How the mesh gets used | Private, per-entity structures | Shared purpose framework |
| What the Synome IS | What the Synome BELIEVES | How the Synome THINKS | How teleonomes live within it | What teleonomes live for |

Macrosynomics defines structures. Synodoxics defines knowledge dynamics and security. Neurosymbolic describes the practical cognition loop — how the architectural commitments in synodoxics translate into concrete mechanism. Synoteleonomics describes the entities that use these mechanisms. The Hearth provides the purpose that drives all four.

Macrosynomics and synodoxics together form the two halves of the dual architecture — the deontic skeleton and the probabilistic mesh. Neurosymbolic operationalizes the epistemic side. Documents frequently cross-reference across all directories.

---

## Summary Documents

Each subdirectory has a companion summary at this level:

| Summary | Source | Status |
|---------|--------|--------|
| [`synomics-summary.md`](synomics-summary.md) | All 28 docs across all subdirectories | Current |
| [`macrosynomics-summary.md`](macrosynomics-summary.md) | 6 docs in `macrosynomics/` | Current |
| [`synodoxics-summary.md`](synodoxics-summary.md) | 6 docs in `synodoxics/` | Current |
| [`synoteleonomics-summary.md`](synoteleonomics-summary.md) | 14 docs in `synoteleonomics/` | Current |
| [`hearth-summary.md`](hearth-summary.md) | 2 docs in `hearth/` | Current |

### Why they exist

The full content of any single subdirectory exceeds what fits in a context window. When working inside one subdirectory, you need awareness of the others — but loading all four is infeasible.

Summaries solve this: load the full documents for the directory you're editing, and load the summaries for the others. This gives you cross-directory context without blowing the context budget.

### How to use them

When working on docs in **one** subdirectory:
1. Load that directory's full source files as needed
2. Load the other summaries for cross-reference context

For example, when editing `synoteleonomics/` docs, load `macrosynomics-summary.md`, `synodoxics-summary.md`, and `hearth-summary.md` alongside the synoteleonomics source files.

### Maintenance

Summaries should be regenerated when the source directory changes materially. Each summary includes a document index at the bottom mapping sections back to source files.
