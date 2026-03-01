# Sentinel Integration

**Last Updated:** 2026-01-27

## Connection to Sentinel

The Risk Framework provides the calculations that sentinel formations and LPHA beacons perform.

For full sentinel specification, see `trading/sentinel-network.md`.
For the broader beacon taxonomy, see `synomics/macrosynomics/beacon-framework.md`.

### Protocol-Level Beacons

| Component | Uses Risk Framework For |
|-----------|-------------------------|
| **lpla-checker** | Calculating CRR per position, TRRC, TRC, Encumbrance Ratio; Settlement cycle processing, LCTS generation handling |

### Prime-Side Sentinels

| Component | Uses Risk Framework For |
|-----------|-------------------------|
| **stl-base** | Risk monitoring during execution, deployment decisions |
| **stl-warden** | Independent risk verification, halt triggers |

### Folio-Side Sentinels

| Component | Uses Risk Framework For |
|-----------|-------------------------|
| **stl-base-{folio}** | Risk monitoring during execution, deployment decisions (automated folios) |
| **stl-warden-{folio}** | Independent risk verification, halt triggers (automated folios) |
| **stl-principal-{owner}** | Direct control with structural protection only (principal control folios) |

> **Note:** Automated folios inherit formation-level protection identical to Primes — TTS is defined by the warden set, and ORC is sized accordingly. Principal control folios have no TTS (no wardens to shut them down) — risk is bounded by rate limits and PAU architecture alone, not by formation-level protections.

### Halo-Side LPHA Beacons

| Component | Uses Risk Framework For |
|-----------|-------------------------|
| **lpha-lcts** | LCTS vault capacity management, redemption processing |
| **lpha-nfat** | NFAT Facility operations, claim processing |

> **Note:** lpha-lcts and lpha-nfat are **LPHA beacons** (deterministic rule execution), not sentinel formations. Prime-side stl-base/stl-stream/stl-warden formations come later and operate Primes; Halo execution remains beacon-driven.

### LPHA Beacons

| Component | Uses Risk Framework For |
|-----------|-------------------------|
| **lpha-halo** | Reporting risk metrics for Halo Units |

### Key Metrics (from Sentinel doc)

| Metric | Definition | Risk Framework Connection |
|--------|------------|---------------------------|
| **CRR** | Capital Ratio Requirement per position | Risk weight if matched; otherwise the forced-loss envelope (e.g., `max(RW, FRTB drawdown)` or `max(RW, gap risk)`) |
| **TRRC** | Total Required Risk Capital | Sum of CRR × position size across portfolio |
| **TRC** | Total Risk Capital actually held | Actual safety capital |
| **Encumbrance Ratio** | TRRC / TRC | Capital utilization — target ≤90% |

---

## PIV / Trading Execution Risk

Sentinel formations that operate Prime Intent Vaults (PIVs) face **trading execution risk** in addition to portfolio risk. Unlike portfolio risk — which is managed through CRR, duration matching, and risk capital — PIV risk is managed through on-chain enforcement mechanisms:

- **Delegated Intent Policy (DIP):** Per-vault policy defining allowed pairs, max slippage, per-intent notional caps, and per-window velocity limits. Enforced at fill time via a stateful vault hook.
- **Per-window caps:** Hourly and daily notional limits bound trading throughput, capping worst-case losses from a malfunctioning or compromised stl-base.
- **EIP-1271 validation:** Settlement contract validates maker authorization against the Prime Intent Vault, ensuring only authorized delegated signers can produce valid intents.
- **Vault balance isolation:** Only the PIV balance is exposed to settlement; the full Prime PAU is not at risk.

PIV trading execution risk is bounded by the vault balance and velocity limits rather than sized through ORC. For the full PIV specification, see `trading/sky-intents.md`. For the ORC/PIV boundary and how operational risk capital relates to formation-level protections, see `risk-framework/operational-risk-capital.md`.

---

## Category Caps (Correlation Framework) Outputs

If category caps are enabled (`correlation-framework.md`), lpla-checker should additionally be able to report:

- Per category `c`: `cap_percent[c]`, `cap_amount[c]`, `exposure_total[c]`, `utilization[c]`
- Per Prime `p`, category `c`: `alloc[p][c]`, `E[p][c]`, `P[p][c]` (penalized amount)
- Portfolio-level: total over-cap exposure and resulting 100%-CRR-required capital
