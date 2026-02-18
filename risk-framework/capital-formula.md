# Capital Formula

**Last Updated:** 2026-01-27

## The Capital Formula

This file defines how the major risk components combine into a single required capital number. Each component’s calibration details live in its own module.

For any Prime's portfolio, total capital requirement is:

```
Total Capital = Σ Position Capital
              + Category Cap Penalties
```

**Per-position calculation:**

For each position with stressed pull-to-par (SPTP):
```
Duration Capacity = Cumulative liability amount in buckets ≥ required bucket
Matched Portion = min(Position Size, Available Duration Capacity)
Unmatched Portion = Position Size - Matched Portion

Matched Capital = Matched Portion × Risk Weight
Unmatched Capital = Unmatched Portion × max(Risk Weight, FRTB Drawdown)

Position Capital = Matched Capital + Unmatched Capital
```

For liquid, tradable positions without pull-to-par (e.g., perpetual spot exposures):
```
Position Capital = Position Size × max(Risk Weight, FRTB Drawdown)
```

For collateralized lending positions (gap risk / liquidation shortfall):
```
Position Capital = Position Size × max(Risk Weight, Gap Risk CRR)
```

**Implementation note:** if the risk weight and forced-loss terms use different exposure bases (e.g., notional vs market value vs EAD), compute both in **dollars** first and apply `max(...)` to the dollar capital amounts.

## Where Each Component Is Defined

- **Duration + matching:** `duration-model.md`, `matching.md`
- **Fundamental risk weight:** `asset-classification.md` (risk weight primitive) + `asset-type-treatment.md` (how it applies)
- **Market risk / FRTB drawdown:** `market-risk-frtb.md`
- **Collateralized lending gap risk:** `collateralized-lending-risk.md`
- **Category caps (concentration limits):** `correlation-framework.md`

**Duration capacity consumption:**
- Positions consume duration capacity in order of matching
- Once capacity at a bucket is consumed, additional positions requiring that bucket are unmatched
- Capacity at longer buckets can match shorter-duration assets (bucket 48 can match an asset with 360-day SPTP)

Category caps enforce concentration limits via 100% CRR on excess (see `correlation-framework.md`).

**Term Halo (NFAT) positions:** For positions held via Term Halo sleeves, CRR varies by sleeve phase — Filling (low), Deploying (high, reflecting information opacity during obfuscated deployment), At Rest (medium, based on attested risk characteristics), with CRR increasing if re-attestation is missed. See `smart-contracts/nfats.md` for the full sleeve-phase CRR model.

**Capital funding:** This formula outputs Total Required Risk Capital (TRRC). For how TRRC is funded across JRC/SRC tiers with ingression-adjusted recognition, see `accounting/risk-capital-ingression.md`.

---
