# Sentinel Integration

**Last Updated:** 2025-12-25

## Connection to Sentinel

The General Risk Framework provides the calculations that Sentinel performs:

| Sentinel Component | Uses GRF For |
|--------------------|--------------|
| **stk-verify** | Calculating CRR per position, TRRC, TRC, Encumbrance Ratio |
| **stl-prime** | Determining capital adequacy, triggering alerts |
| **stl-halo** | Reporting risk metrics for Halo Units |

### Key Metrics (from Sentinel doc)

| Metric | Definition | GRF Connection |
|--------|------------|----------------|
| **CRR** | Capital Ratio Requirement per position | Risk weight (if matched) or FRTB drawdown (if unmatched) |
| **TRRC** | Total Required Risk Capital | Sum of CRR × position size across portfolio |
| **TRC** | Total Risk Capital actually held | Actual safety capital |
| **Encumbrance Ratio** | TRRC / TRC | Capital utilization — target ≤90% |

---

## Category Caps (Correlation Framework) Outputs

If category caps are enabled (`correlation-framework.md`), Sentinel should additionally be able to report:

- Per category `c`: `cap_percent[c]`, `cap_amount[c]`, `exposure_total[c]`, `utilization[c]`
- Per Prime `p`, category `c`: `alloc[p][c]`, `E[p][c]`, `P[p][c]` (penalized amount)
- Portfolio-level: total over-cap exposure and resulting 100%-CRR-required capital
