# Risk Framework

**Status:** Draft
**Last Updated:** 2026-01-27

This folder contains the modular Risk Framework documentation.

## Module Index

- `duration-model.md` — Liability duration analysis (Lindy Duration Model), Duration Buckets, structural caps, capacity reservation.
- `asset-classification.md` — Asset characteristics: fundamental risk, drawdown risk, stressed pull-to-par (SPTP).
- `matching.md` — Rate risk vs credit spread risk, duration matching eligibility, matched/unmatched treatment, partial matching.
- `asset-type-treatment.md` — Worked treatments for major asset types (TradFi, crypto lending, hybrid).
- `collateralized-lending-risk.md` — Jump-to-default + liquidation loss (gap risk) for collateralized lending.
- `market-risk-frtb.md` — FRTB-style drawdown treatment for unmatched liquid assets.
- `asc.md` — Actively Stabilizing Collateral (ASC): ALM requirements, latent/resting liquidity, renting, peg defense.
- `capital-formula.md` — Capital formulas and computation flow.
- `correlation-framework.md` — Category caps + capacity rights (concentration limits via 100% CRR on excess).
- `examples.md` — Current vs proposed examples + summary principles.
- `operational-risk-capital.md` — Operational Risk Capital (ORC): executor-posted capital covering compromise damage, rate limit ↔ capital linkage.
- `sentinel-integration.md` — Output metrics and how Sentinel uses the framework.
- `risk-monitoring.md` — Risk monitoring framework — metrics, stress testing, anomaly detection, escalation procedures.

## Related: Accounting

Settlement operations, auction mechanics, and capital recognition live in `accounting/`:

- `../accounting/daily-settlement-cycle.md` — Daily settlement timeline, auctions, LCTS settlement.
- `../accounting/tugofwar.md` — Tug-of-war algorithm for duration capacity allocation.
- `../accounting/risk-capital-ingression.md` — How external capital is recognized on Prime balance sheets.

## Core Principle

Capital requirements should reflect: **what is the maximum loss we could be forced to realize?**

## Open Items

1. **Correlation framework specifics** — Stress calibration, multi-group assets, aggregation method
2. **Data infrastructure** — How to track USDS lot ages for liability duration analysis
3. ~~**Halo Unit treatment**~~ — Halo Units are look-through to underlying assets. A Halo Unit backed by 2-year ABS is treated as FRTB (if unmatched) or Risk Weight (if liability-matched). No special Halo-level treatment needed; use `asset-classification.md` and `matching.md`
4. ~~**Rate limit integration**~~ — Addressed in `operational-risk-capital.md`
5. **Beacon implementation** — Formulas and algorithms for lpla-checker calculations

---

### Token Standards
| Document | Relevance |
|----------|-----------|
| `../smart-contracts/lcts.md` | LCTS tokens (srUSDS, TEJRC, TISRC) are risk capital instruments sized by this framework |

*This document defines the Risk Framework. For Sentinel integration details, see the Sentinel Network document.*
