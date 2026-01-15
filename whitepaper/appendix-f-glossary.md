# Appendix F: Glossary

Term definitions for the Sky Protocol.

---

## Core Concepts

| Term | Definition |
|------|------------|
| **Sky** | Evolution of MakerDAO; decentralized stablecoin protocol |
| **Sky Core** | Central governance and monetary policy layer |
| **Atlas** | Constitutional governance document; human-readable; ~10-20 pages |
| **Synome** | Machine-readable operational database; contains all parameters and configurations |

---

## Tokens

| Term | Definition |
|------|------------|
| **USDS** | Sky stablecoin; 1:1 with USD |
| **sUSDS** | Savings token; earns Sky Savings Rate |
| **SKY** | Governance token; staking rewards + buyback/burn |
| **stUSDS** | Segregated risk capital for SKY-backed borrowing |
| **DAI** | Legacy stablecoin; 1:1 convertible with USDS |
| **MKR** | Legacy governance token; 1:24000 convertible with SKY |

---

## Rates

| Term | Definition |
|------|------------|
| **SSR** | Sky Savings Rate — base yield for sUSDS holders |
| **Base Rate** | Protocol-wide stability fee |
| **Sky Borrow Rate** | Rate for borrowing USDS against SKY |
| **stUSDS Rate** | Yield for stUSDS holders (higher than SSR) |

---

## Stability

| Term | Definition |
|------|------------|
| **ALM** | Asset Liability Management — liquidity and portfolio rules that support the USDS peg |
| **ASC** | Actively Stabilizing Collateral — highly liquid non‑USDS assets used to buy USDS during downward peg pressure |
| **DAB** | Demand Absorption Buffer — highly liquid USDS (or USDS-equivalent) positions used to sell USDS during upward peg pressure |
| **LitePSM** | Lite Peg Stability Module — 1:1 USDC ↔ USDS conversion backstop used for peg stability |

---

## Treasury

| Term | Definition |
|------|------------|
| **TMF** | Treasury Management Function — the waterfall that allocates protocol net revenue to security/operations, buffers, burn, and staking rewards |
| **Smart Burn Engine** | Automated mechanism that executes SKY buybacks (and related actions) using TMF-allocated surplus |
| **Aggregate Backstop Capital** | Protocol solvency buffer for bad debt protection (targeted as a percentage of USDS liabilities) |
| **Fortification Conserver** | Treasury allocation for legal defense and unquantifiable risk management (often implemented via a designated foundation/entity) |
| **Net Revenue Ratio** | Scaling factor used in TMF allocations that increases with protocol net revenue (higher revenue increases allocations to certain steps like burn/fortification) |

---

## Agents

| Term | Definition |
|------|------------|
| **Agent** | Autonomous entity operating within Sky's framework |
| **Prime** | Capital-deploying agent category; heavyweight |
| **Halo** | Lighter operational agent; wraps external value |
| **Executor** | Governance operator agent; performs privileged operations |
| **Generator** | Layer interfacing with stablecoin ERC20 contract |
| **Generator Agent** | Agent that issues Sky Generated Assets (SGAs); future growth vector |

---

## Agent Components

| Term | Definition |
|------|------------|
| **SubProxy** | On-chain treasury controlled by an Agent |
| **Agent Artifact** | Governance documentation for an Agent |
| **Agent Token** | Native token for an Agent (10B supply, no emissions) |
| **Nested Contributors** | Core contributors serving both Agent and Sky |
| **Foundation** | Legal entity associated with a Prime |

---

## Risk Capital

| Term | Definition |
|------|------------|
| **JRC** | Junior Risk Capital — first to absorb losses |
| **SRC** | Senior Risk Capital — absorbs after JRC depleted |
| **IJRC** | Internal JRC — Prime's own capital |
| **EJRC** | External JRC — from other parties |
| **TEJRC** | Tokenized External JRC |
| **TISRC** | Tokenized Isolated SRC (per-Prime) |
| **srUSDS** | Senior Risk USDS — global senior risk capital |
| **Encumbrance Ratio** | Required Risk Capital / Total Risk Capital |
| **First Loss Capital (FLC)** | The first 10% of JRC losses, absorbed solely by Prime's own capital (IJRC) before external JRC shares losses |

---

## Infrastructure

| Term | Definition |
|------|------------|
| **PAU** | Parallelized Allocation Unit — standard building block (Controller + ALMProxy + RateLimits) |
| **BEAM** | Bounded External Access Module — authorized role with constraints |
| **pBEAM** | Process BEAM — directly impacts contracts |
| **cBEAM** | Configurator BEAM — modifies pBEAM guardrails |
| **aBEAM** | Admin BEAM — grants cBEAMs, creates inits |
| **SORL** | Second-Order Rate Limit — constraint on rate limit increase speed |
| **Init** | Pre-approved configuration that GovOps can instantiate |

---

## Settlement

| Term | Definition |
|------|------------|
| **LCTS** | Liquidity Constrained Token Standard — queue-based conversion |
| **Generation** | LCTS grouping; users in same generation share capacity proportionally |
| **Settlement Cycle** | Weekly: Tue lock → Wed settle (post-Laniakea) |
| **OSRC Auction** | Sealed-bid auction for Senior Risk Capital capacity |
| **SPTP** | Stressed Pull-to-Par — time until asset converges to fundamental value |

---

## Governance

| Term | Definition |
|------|------------|
| **Governance Poll** | SKY holder decision-making; 3-day duration |
| **Executive Vote** | On-chain parameter changes |
| **Aligned Delegate** | Recognized governance participant; ranked L1-L3 |
| **Facilitator** | Interprets Atlas on behalf of Executors |
| **Root Edit** | Process for token holders to modify Agent Artifacts |
| **Core Council** | Group of Executors responsible for Sky Core operations |

---

## Sentinel Network

| Term | Definition |
|------|------------|
| **Sentinel** | Autonomous system operating within Sky |
| **stl-* (prefix)** | Sentinel interface specification |
| **stk-* (prefix)** | Sentinel toolkit operation |
| **Synome** | Decentralized database for operational data |
| **CC Synome** | Core Council Synome — authoritative source |

---

## Risk Framework

| Term | Definition |
|------|------------|
| **GRF (deprecated)** | Former monolithic “General Risk Framework”; replaced by the modular Risk Framework docs in `active/risk-framework/` |
| **SPTP Bucket** | Time bucket for liability matching (0.5 months each) |
| **Lindy-Based Demand** | Liability duration estimation based on current age |
| **Risk Weight** | Capital for fundamental risk (credit, smart contract) |
| **FRTB Drawdown** | Capital for full mark-to-market drawdown |

---

## Multi-Chain

| Term | Definition |
|------|------------|
| **SkyLink** | Multi-chain token bridging infrastructure |
| **Foreign Prime** | Prime equivalent on altchains |
| **Foreign Halo** | Halo equivalent on altchains |
| **CCTP** | Circle Cross-Chain Transfer Protocol |

---

## Legacy

| Term | Definition |
|------|------------|
| **MakerDAO** | Original protocol; evolved into Sky |
| **Vault** | Original collateralized debt position |
| **DSR** | DAI Savings Rate; predecessor to SSR |
| **PSM** | Peg Stability Module |
