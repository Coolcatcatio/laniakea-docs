# Sky Agents

Specifications for the four Synomic Agent types in the Sky ecosystem: Generators, Primes, Halos, and Guardians. Each agent type serves a distinct role in the capital flow hierarchy, from foundational credit creation (Generator) through capital allocation (Primes) to product-level deployment (Halos), with operational stewardship by Guardians.

This directory also covers the lifecycle mechanics — how agents are created and restructured.

## Subdirectories

| Directory | Description |
|---|---|
| [`halo-agents/`](halo-agents/) | Halo specifications — the fractal layer (Portfolio, Term, Trading, Exchange, Identity Network, Staking) |
| [`prime-agents/`](prime-agents/) | Prime specifications — heavyweight capital allocators (Spark, Grove, Keel, Obex) |
| [`generator-agents/`](generator-agents/) | Generator specifications — foundational agents that create the credit medium |
| [`guardian-agents/`](guardian-agents/) | Guardian specifications — privileged operation agents with collateral backing |
| [`agent-creation-restructuring/`](agent-creation-restructuring/) | Agent creation and restructuring mechanics (Guardian Accord, Type 1/Type 2 restructures) |

## Agent Type Overview

| Agent Type | Role | Count | Key Property |
|---|---|---|---|
| **Generator** | Creates credit medium (USDS) | Singular per stablecoin | Foundational — all others depend on it |
| **Prime** | Allocates capital at scale | Few (4 operational) | Heavyweight — billions under management |
| **Halo** | Wraps value into products | Many (fractal proliferation) | Flexible — from minimal to complex |
| **Guardian** | Performs privileged operations | Multiple per role type | Accountable — collateral-backed with slashing |

## Related

- [`smart-contracts/architecture-overview.md`](../smart-contracts/architecture-overview.md) — Four-layer capital flow architecture that agents operate within
- [`synomics/macrosynomics/synomic-agents.md`](../synomics/macrosynomics/synomic-agents.md) — Theoretical framework for Synomic Agents
- [`synomics/macrosynomics/beacon-framework.md`](../synomics/macrosynomics/beacon-framework.md) — Beacon taxonomy through which agents are operated
- [`roadmap/`](../roadmap/) — Phased deployment of agent infrastructure (factories in Phases 5-8)
