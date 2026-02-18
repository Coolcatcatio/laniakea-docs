# Duration Model (Demand Side)

**Last Updated:** 2026-01-28

## Liability Duration Analysis (Demand Side)

### Purpose

Determine how much of the USDS liability base is short-term (could demand liquidity soon) versus long-term (sticky, unlikely to redeem).

### Method: Lindy Duration Model

For each lot of USDS:
1. Measure current age (time since last transfer)
2. Expected remaining holding time = current age × Lindy factor
3. Apply conservative haircut (e.g., 0.5x or 0.7x instead of 1x pure Lindy)

### Duration Bucket Structure

The Duration Bucket system uses a two-layer capacity calculation:
1. **Daily Lindy Measurement** — Dynamic calculation of liability duration distribution
2. **Structural Maximum Caps** — Governance-set upper limits per bucket, derived from empirical bank run research

#### Bucket Definitions

The system uses **101 buckets**, each representing **15 days**:

| Bucket | Duration | Bucket | Duration |
|--------|----------|--------|----------|
| 0 | 0 days | 50 | 750 days |
| 1 | 15 days | ... | ... |
| 2 | 30 days | 84 | 1,260 days (JAAA) |
| ... | ... | 100 | 1,500+ days |

**Bucket semantics:**
- **Liability side:** Bucket N contains liabilities with expected remaining duration ≥ N × 15 days
- **Asset side:** Bucket N is required for assets with SPTP (Stressed Pull-to-Par) in the range [(N-1) × 15, N × 15) days
- **Bucket 100:** Captures all liabilities with expected duration ≥ 1,500 days (the structural/permanent base)

#### Structural Maximum Caps: Double Exponential Model

The structural caps follow a **double exponential decay** model calibrated to empirical bank run data:

```
Individual Cap(t) = A × e^(-λ₁ × t) + B × e^(-λ₂ × t)
```

**Research-Calibrated Parameters:**

| Parameter | Value | Meaning |
|-----------|-------|---------|
| **A** | 10% | Hot money amplitude |
| **λ₁** | 0.35 | Hot money decay rate (half-life = 1.0 months) |
| **B** | 0.70% | Sticky money amplitude |
| **λ₂** | 0.0175 | Sticky money decay rate (half-life = 19.8 months) |

**Empirical Calibration Basis:**

The parameters were fitted to match the aggressive end of empirical bank run research:

| Horizon | Target | Empirical Basis |
|---------|--------|-----------------|
| 1 month | 75% | SVB lost 25% in 1 day, 87% over 2 days; First Republic lost 37% in 2 days |
| 3 months | 55% | First Republic: 57% gone by end Q1 2023; Credit Suisse: 29% deposits gone Q1 |
| 6 months | 45% | Credit Suisse: ~40% over 6 months |
| 12 months | 35% | NSFR implies 5-10% retail runoff/year, but 50%+ wholesale |
| 24 months | 25% | Beyond 1 year, only structural holders remain |
| 36 months | 15% | Deep Lindy territory |
| 50+ months | 10% | Structural/permanent base |

**Key Research Sources:**
- Basel III LCR/NSFR frameworks
- March 2023 bank runs: SVB, Signature Bank, First Republic, Credit Suisse
- MMF crisis data: September 2008 (26% in 2 weeks), March 2020 (30% in 2 weeks)
- ECB/Fed deposit behavior studies

#### Full Bucket Table

| Bucket | Days  | Individual | Cumulative |     | Bucket | Days  | Individual | Cumulative |
| ------ | ----- | ---------- | ---------- | --- | ------ | ----- | ---------- | ---------- |
| 0      | 0     | 14.4061%   | 100.0000%  |     | 51     | 765   | 0.3861%    | 22.3357%   |
| 1      | 15    | 10.4138%   | 85.5939%   |     | 52     | 780   | 0.3794%    | 21.9496%   |
| 2      | 30    | 7.5959%    | 75.1801%   |     | 53     | 795   | 0.3728%    | 21.5703%   |
| 3      | 45    | 5.6057%    | 67.5843%   |     | 54     | 810   | 0.3663%    | 21.1975%   |
| 4      | 60    | 4.1988%    | 61.9786%   |     | 55     | 825   | 0.3600%    | 20.8312%   |
| 5      | 75    | 3.2031%    | 57.7798%   |     | 56     | 840   | 0.3537%    | 20.4712%   |
| 6      | 90    | 2.4972%    | 54.5766%   |     | 57     | 855   | 0.3476%    | 20.1175%   |
| 7      | 105   | 1.9956%    | 52.0794%   |     | 58     | 870   | 0.3415%    | 19.7699%   |
| 8      | 120   | 1.6381%    | 50.0838%   |     | 59     | 885   | 0.3356%    | 19.4284%   |
| 9      | 135   | 1.3821%    | 48.4457%   |     | 60     | 900   | 0.3298%    | 19.0928%   |
| 10     | 150   | 1.1977%    | 47.0637%   |     | 61     | 915   | 0.3241%    | 18.7630%   |
| 11     | 165   | 1.0639%    | 45.8660%   |     | 62     | 930   | 0.3185%    | 18.4389%   |
| 12     | 180   | 0.9658%    | 44.8020%   |     | 63     | 945   | 0.3129%    | 18.1204%   |
| 13     | 195   | 0.8930%    | 43.8362%   |     | 64     | 960   | 0.3075%    | 17.8075%   |
| 14     | 210   | 0.8379%    | 42.9432%   |     | 65     | 975   | 0.3022%    | 17.5000%   |
| 15     | 225   | 0.7955%    | 42.1053%   |     | 66     | 990   | 0.2969%    | 17.1978%   |
| 16     | 240   | 0.7621%    | 41.3098%   |     | 67     | 1005  | 0.2918%    | 16.9009%   |
| 17     | 255   | 0.7350%    | 40.5477%   |     | 68     | 1020  | 0.2867%    | 16.6091%   |
| 18     | 270   | 0.7125%    | 39.8127%   |     | 69     | 1035  | 0.2817%    | 16.3224%   |
| 19     | 285   | 0.6933%    | 39.1002%   |     | 70     | 1050  | 0.2769%    | 16.0407%   |
| 20     | 300   | 0.6764%    | 38.4069%   |     | 71     | 1065  | 0.2721%    | 15.7638%   |
| 21     | 315   | 0.6613%    | 37.7305%   |     | 72     | 1080  | 0.2673%    | 15.4918%   |
| 22     | 330   | 0.6474%    | 37.0692%   |     | 73     | 1095  | 0.2627%    | 15.2244%   |
| 23     | 345   | 0.6345%    | 36.4218%   |     | 74     | 1110  | 0.2581%    | 14.9617%   |
| 24     | 360   | 0.6223%    | 35.7874%   |     | 75     | 1125  | 0.2537%    | 14.7036%   |
| 25     | 375   | 0.6106%    | 35.1651%   |     | 76     | 1140  | 0.2493%    | 14.4499%   |
| 26     | 390   | 0.5994%    | 34.5545%   |     | 77     | 1155  | 0.2449%    | 14.2007%   |
| 27     | 405   | 0.5886%    | 33.9550%   |     | 78     | 1170  | 0.2407%    | 13.9557%   |
| 28     | 420   | 0.5781%    | 33.3664%   |     | 79     | 1185  | 0.2365%    | 13.7151%   |
| 29     | 435   | 0.5679%    | 32.7883%   |     | 80     | 1200  | 0.2324%    | 13.4786%   |
| 30     | 450   | 0.5579%    | 32.2204%   |     | 81     | 1215  | 0.2284%    | 13.2461%   |
| 31     | 465   | 0.5481%    | 31.6625%   |     | 82     | 1230  | 0.2244%    | 13.0178%   |
| 32     | 480   | 0.5385%    | 31.1144%   |     | 83     | 1245  | 0.2205%    | 12.7934%   |
| 33     | 495   | 0.5291%    | 30.5759%   |     | 84     | 1260  | 0.2167%    | 12.5728%   |
| 34     | 510   | 0.5199%    | 30.0468%   |     | 85     | 1275  | 0.2129%    | 12.3561%   |
| 35     | 525   | 0.5109%    | 29.5269%   |     | 86     | 1290  | 0.2092%    | 12.1432%   |
| 36     | 540   | 0.5020%    | 29.0160%   |     | 87     | 1305  | 0.2056%    | 11.9340%   |
| 37     | 555   | 0.4933%    | 28.5140%   |     | 88     | 1320  | 0.2020%    | 11.7284%   |
| 38     | 570   | 0.4847%    | 28.0207%   |     | 89     | 1335  | 0.1985%    | 11.5263%   |
| 39     | 585   | 0.4763%    | 27.5360%   |     | 90     | 1350  | 0.1951%    | 11.3278%   |
| 40     | 600   | 0.4680%    | 27.0597%   |     | 91     | 1365  | 0.1917%    | 11.1327%   |
| 41     | 615   | 0.4599%    | 26.5917%   |     | 92     | 1380  | 0.1884%    | 10.9410%   |
| 42     | 630   | 0.4519%    | 26.1318%   |     | 93     | 1395  | 0.1851%    | 10.7526%   |
| 43     | 645   | 0.4441%    | 25.6799%   |     | 94     | 1410  | 0.1819%    | 10.5675%   |
| 44     | 660   | 0.4364%    | 25.2358%   |     | 95     | 1425  | 0.1787%    | 10.3856%   |
| 45     | 675   | 0.4288%    | 24.7995%   |     | 96     | 1440  | 0.1756%    | 10.2068%   |
| 46     | 690   | 0.4214%    | 24.3707%   |     | 97     | 1455  | 0.1726%    | 10.0312%   |
| 47     | 705   | 0.4141%    | 23.9493%   |     | 98     | 1470  | 0.1696%    | 9.8586%    |
| 48     | 720   | 0.4069%    | 23.5352%   |     | 99     | 1485  | 0.1667%    | 9.6890%    |
| 49     | 735   | 0.3998%    | 23.1284%   |     | 100    | 1500+ | 9.5223%    | 9.5223%    |
| 50     | 750   | 0.3929%    | 22.7286%   |     |        |       |            |            |

**Reading the table:**
- **Individual:** Maximum % of portfolio that can be in this specific bucket alone
- **Cumulative:** Maximum % of portfolio that can be in this bucket OR higher (used for asset matching)
- **Bucket 100:** The 9.52% cumulative includes the tail beyond 1,500 days — the structural/permanent holder base

#### Key Checkpoints

| Horizon | Bucket | Cumulative | ~% Gone | Interpretation |
|---------|--------|------------|---------|----------------|
| **30 days** | 2 | 75.2% | 25% | Acute stress phase |
| **90 days** | 6 | 54.6% | 45% | Peak stress; nearly half gone |
| **180 days** | 12 | 44.8% | 55% | Post-acute; committed holders remain |
| **360 days** | 24 | 35.8% | 64% | Survived full stress cycle |
| **720 days** | 48 | 23.5% | 76% | Structural holders only |
| **1,080 days** | 72 | 15.5% | 85% | Deep Lindy territory |
| **1,260 days (JAAA)** | 84 | 12.6% | 87% | Duration capacity for CLO AAA |
| **1,500+ days** | 100 | 9.5% | 90% | Permanent/structural base |

*Note: These caps represent maximum allowable allocation even if Lindy measurement suggests higher capacity. Governance may adjust parameters based on observed USDS holder behavior.*

#### Two-Layer Capacity Calculation

**Layer 1: Daily Lindy Measurement**
Every day, measure USDS lot ages and calculate expected remaining duration to produce a "raw" liability distribution.

**Layer 2: Apply Structural Caps**
For each bucket from longest to shortest:
```
Raw Capacity = Lindy-measured liability amount for this bucket
Cap = Max Cap % × Total Portfolio

If Raw Capacity > Cap:
  Effective Capacity = Cap
  Overflow = Raw Capacity - Cap
  → Overflow trickles down to next-lower bucket
Else:
  Effective Capacity = Raw Capacity
```

**Example:**
- Total portfolio: $10B
- Lindy says bucket 48 (720 days) has $500M (5% of portfolio)
- Bucket 48 cap is 2% = $200M
- Result: Bucket 48 gets $200M, remaining $300M trickles to bucket 45 (675 days)
- If bucket 45 also exceeds its cap after adding overflow, it trickles further down

#### Conservative Rounding Rules

| Side | Rule | Rationale |
|------|------|-----------|
| **Liabilities** | Round DOWN to nearest bucket | A 40-day liability → bucket 2 (30 days). Conservative: assumes earlier redemption. |
| **Assets** | Round UP to nearest bucket | An asset with 1,250-day SPTP → bucket 84 (1,260 days). Conservative: requires longer-duration liabilities. |

#### Cumulative Capacity for Matching

An asset can match against its required bucket AND all higher buckets. Higher-tier capacity can always fulfill lower-tier requirements.

**Example:**
- An asset with 360-day SPTP requires bucket 24
- Available capacity = bucket 24 + bucket 30 + bucket 36 + ... + bucket 48 (cumulative)
- A 720-day liability can match a 360-day asset (but not vice versa)

```
Cumulative Capacity at Bucket N = Σ (Effective Capacity for all buckets ≥ N)
```

---

### Duration Capacity Reservation System

Duration Bucket capacity is allocated to Primes through a reservation system. Primes acquire reservations via daily auctions, then can resell them on a secondary market.

#### Core Principles

1. **Own-bucket priority is emergent** — Primes tug at their own bucket with distance 0 (no decay), giving them natural priority without a separate allocation phase (see `../accounting/tugofwar.md`)
2. **All capacity allocated via tug-of-war** — When Lindy doesn't match reservations, the tug-of-war mechanism redistributes capacity to Primes with unmet need
3. **Full secondary market flexibility** — Time-sliced ownership, partial amounts, arbitrary durations

#### Daily Cycle

| Event | Frequency | Description |
|-------|-----------|-------------|
| Lindy measurement | Daily | Measure USDS lot ages, calculate liability duration distribution |
| Duration auctions | Daily | Auction unreserved capacity in each bucket |
| Tug-of-war | Daily | Allocate all capacity (own-bucket priority emergent from distance-0 tugging) |
| Settlement | Daily | Process deposits, redemptions, yield distribution |

#### Primary Auction

Auctions occur when unreserved capacity exists at a bucket. Primes bid price-per-epoch for capacity amounts. Highest bidders win. Winners receive reservations starting next epoch.

#### Secondary Market

Reservation holders can sell portions of their ownership with time-sliced schedules. Buyers can immediately resell, enabling complex ownership structures.

#### Capacity Allocation

All capacity is allocated through tugging — there's no special "own bucket first" phase. Primes tug at their own bucket with distance 0 (no decay), giving them natural priority there. But if their bucket is empty and a neighbor is full, they can still effectively tug nearby buckets.

**Phase 1: Tug-of-War**

All Primes tug for capacity simultaneously:
- Distance 0 (own bucket) = full tug strength, maximum effective value
- Distance N = tug decays by 0.9^N (floor at 10%)
- Tugging UP = effective value 1.0
- Tugging DOWN = effective value = target/your bucket
- Collisions resolved pro-rata
- Multiple rounds until all needs met or capacity exhausted

**Phase 2: Trading**

After needs are met, Primes can trade up:
- Tug at higher buckets with remaining excess
- Release lowest-value capacity downward
- Cascade until capacity finds a Prime that values it

**See `accounting/tugofwar.md` for full algorithm details.**

#### Capacity Duration Rules

| How Acquired | Duration You Get |
|--------------|------------------|
| From own bucket (distance 0) | Your bucket's duration (full match) |
| Tugged from higher bucket | Source bucket's duration (overkill but fine) |
| Tugged from lower bucket | Source bucket's duration (creates gap → gap capital required) |

---
