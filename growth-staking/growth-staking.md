# Growth Staking

**Status:** Draft concept
**Date:** February 2026

---

## Overview

Growth Staking aligns SKY governance token holders with ecosystem innovation. Under Growth Staking, SKY stakers must also hold other Sky Ecosystem tokens — called **growth assets** — to unlock staking rewards. The more directly a growth asset contributes to ecosystem innovation, the less of it a staker needs to hold.

This creates a direct economic link between governance participation (staking SKY) and investment in the ecosystem's growth layer (holding Agent tokens, providing risk capital, or holding sSGA).

---

## The Staking Factor

Each eligible growth asset has a **Staking Factor (SF)** — the ratio of growth asset value to staked SKY value required to unlock full staking rewards.

| Category | Assets | SF | Rationale |
|---|---|---|---|
| **Agent tokens** | Prime tokens (SPK, Grove, Keel, Obex), Halo Agent tokens, Generator Agent tokens, Guardian Agent tokens | 0.4× | Equity in the innovators; most direct growth signal |
| **Junior risk capital** | TEJRC (per-Prime) | 0.6× | First-loss capital enabling Primes to deploy; binding constraint on ingression |
| **Senior risk capital** | srSGA (global), TISRC (per-Prime) | 1.0× | Funds the system but loss-protected; less direct innovation contribution |
| **Savings** | sSGA | 2.0× | Passive participation; already incentivized via savings rate |

**Excluded assets:** stUSDS, Halo Unit shares, NFATs, LCTS queue positions, DAI, MKR.

### Reward Scaling

Rewards scale linearly from 0% to 100% based on how much of the staking factor requirement is satisfied:

```
Reward % = min(1, Growth Asset SF Value / Staked SKY Value)
```

Where **Growth Asset SF Value** = Growth Asset Value / SF for that asset type.

**Example** — $100k staked SKY, with Prime tokens at SF 0.4×:

| Prime tokens held | Requirement ($100k × 0.4) | Reward % |
|---|---|---|
| $0 | $40k | 0% |
| $20k | $40k | 50% |
| $40k | $40k | 100% |

When multiple growth assets are held, each is converted to its SF-adjusted value and the contributions are summed:

```
Total SF Value = Σ (Asset Value_i / SF_i)

Reward % = min(1, Total SF Value / Staked SKY Value)
```

**Example** — $100k staked SKY, holding $20k SPK (SF 0.4) + $30k srSGA (SF 1.0):

```
SF Value = ($20k / 0.4) + ($30k / 1.0) = $50k + $30k = $80k
Reward % = min(1, $80k / $100k) = 80%
```

---

## Agent Token Valuation

Agent tokens are valued for SF purposes at the **lower of book value or market value** per token. This prevents market price manipulation from inflating staking factor calculations.

### Prime Agent Tokens

Book value = **net capital reserves** of the Prime, with look-through to book value for any Halo Agent tokens the Prime holds.

A Prime holding $500M in capital reserves with 10B tokens outstanding has a book value of $0.05 per token. If the market price is $0.08, the SF calculation uses $0.05. If the market price is $0.03, the SF calculation uses $0.03.

### Halo Agent Tokens

Halo book value combines **capital reserves** and **earnings × P/E ratio**. Halos invest in technology and infrastructure, so their value reflects both the capital they hold and the revenue they generate from it.

```
Halo Book Value = Capital Reserves + (Annual Earnings × P/E)
```

- Initial P/E ratio: **15×**
- Revised periodically through governance

**Early-stage Halos:** A newly capitalized Halo with no earnings history can still count toward the favorable Agent token SF — provided its synomic artifacts demonstrate that the capital is being actively spent on genuine growth (building technology, deploying infrastructure, etc.). This is a qualitative assessment based on observable synomic activity, not just capital sitting idle. Once earnings materialize, the P/E component takes over as the primary value driver.

### Risk Capital and sSGA

TEJRC, TISRC, srSGA, and sSGA are valued at their on-chain redemption value — no book value adjustment needed, since these tokens are directly backed by underlying capital.

---

## The Staking Halo

To participate in Growth Staking, a SKY holder creates a **Staking Halo** — a personal Halo Agent that serves as a self-contained staking and investment vehicle.

### Architecture

```
┌─────────────────────────────────────┐
│           Staking Halo              │
│                                     │
│  ┌───────────────────────────────┐  │
│  │             PAU               │  │
│  │                               │  │
│  │  Staked SKY: 100,000 SKY     │  │
│  │                               │  │
│  │  Growth Assets:               │  │
│  │    SPK:    $30,000            │  │
│  │    TEJRC:  $15,000            │  │
│  │    srSGA:  $10,000            │  │
│  │                               │  │
│  │  Pending Rewards: ...         │  │
│  └───────────────────────────────┘  │
│                                     │
│  Control: Sentinel or Manual        │
└─────────────────────────────────────┘
```

- **Creation** — Instant. Any SKY holder can create a Staking Halo at any time.
- **PAU** — The Staking Halo's internal Protocol Allocation Unit holds both staked SKY and the growth asset portfolio.
- **Reward distribution** — At each daily settlement, the system measures the total staking factor of assets inside the PAU and airdrops the corresponding staking reward.
- **Control** — The holder can manage the growth asset portfolio manually, or configure a **Sentinel** to execute an automated strategy (e.g., "maintain 0.5× in SPK, rebalance when drift exceeds 10%").

### Compounding

The Staking Halo can reinvest rewards automatically:

1. **When SF capacity exists** (growth assets support more SKY than currently staked) — use rewards to acquire more SKY and stake it inside the Halo. This compounds the governance position.

2. **When at SF capacity** (no room for additional SKY without more growth assets) — use rewards to acquire growth assets, expanding capacity for future SKY compounding.

This alternation creates a natural flywheel:

```
Stake SKY → earn rewards → buy growth assets → unlock capacity →
    stake more SKY → earn more rewards → ...
```

A Sentinel can automate this cycle, optimizing the balance between SKY accumulation and growth asset acquisition based on current SF utilization.

---

## Agent-Internal Growth Staking

Primes and Halos that hold SKY in their treasuries automatically earn Growth Staking rewards — the Agent's own book value counts as its growth asset portfolio at SF 0.4×. No separate Staking Halo is needed; the Agent itself functions as one.

A Prime effectively counts as a SKY staker with all of its own tokens in its Staking Halo. Same for a Halo holding SKY.

**Example** — A Prime with $500M book value holding $10M SKY:

```
SF value = $500M / 0.4 = $1.25B effective
Reward % on $10M SKY = min(1, $1.25B / $10M) = 100%
```

Any Agent with meaningful book value trivially satisfies the growth requirement, making SKY holdings essentially free yield for Agents. This creates a structural incentive for Primes and Halos to accumulate SKY in their treasuries — aligning Agent operations with protocol governance and creating natural demand for SKY from the innovation layer itself.

### Double-counting

This creates an accepted paradox: an Agent's book value is used twice for staking factor purposes — once by the Agent itself (to unlock staking rewards on its own SKY holdings), and a second time by external token holders (who hold the Agent's tokens as growth assets in their Staking Halos). The same underlying book value supports both claims. This is by design — the double-counting amplifies the incentive to build genuine book value within Agents, and the protocol accepts this as a worthwhile tradeoff for the alignment it creates.

---

## Incentive Effects

### Capital flow from passive to active

Growth Staking creates a direct incentive to convert passive holdings into active innovation investment:

```
$100k sSGA   → SF value: $100k / 2.0 = $50k effective
    ↓ invest into a Prime
$100k in Prime book value → SF value: $100k / 0.4 = $250k effective
```

Same capital, 5× the staking factor efficiency. This pulls capital from the savings layer into the Agent layer.

### Ecosystem alignment

- **SKY whales** must become ecosystem participants, not passive governance holders
- **Agent token demand** is structurally supported by stakers seeking efficient SF
- **Risk capital supply** increases as stakers invest in TEJRC/SRC for SF credit
- **Mercenary staking** is eliminated — holding SKY alone earns nothing

### Natural segmentation

- Risk-tolerant stakers → hold Agent tokens (SF 0.4×) → maximum capital efficiency
- Moderate stakers → hold TEJRC/SRC (SF 0.6–1.0×) → balanced approach
- Conservative stakers → hold sSGA (SF 2.0×) → still works, just requires more capital

---

## Anti-Gaming

The primary defense against manipulation is the **book value floor** on Agent tokens — you cannot inflate SF value by pumping market prices above book value.

A secondary concern is **hollow agents** — Primes or Halos created solely to warehouse capital and claim favorable SF without genuine innovation activity. Defenses:

1. **Mechanical (day one):** Book value is based on actual capital reserves (Primes) or earnings (Halos), so capital must be genuinely deployed or revenue genuinely earned.
2. **Synomic monitoring (when needed):** Governance can monitor the synomic artifacts of Primes and Halos. A real level of intelligent synomic activity must be observed for an Agent's tokens to maintain SF eligibility. This monitoring layer is deferred — implemented only when manipulation attempts actually occur.

The same monitoring applies to potentially hollow TEJRC positions.

---

## Open Design Questions

- **SF governance** — Are Staking Factors set by governance vote, or derived from on-chain metrics?
- **Halo P/E revision cadence** — How often is the P/E ratio updated? Quarterly governance vote?
- **New Agents with no history** — A new Prime with zero capital reserves or a new Halo with zero earnings would have zero book value. Intended? (Likely yes — prove value before getting SF credit.)
- **Measurement timing** — Snapshot at daily settlement, or time-weighted average to prevent flash-positioning?
- **Forfeited rewards** — Where do unclaimed rewards (from stakers below 100% SF) flow? Back to TMF waterfall? Redistributed to fully-qualifying stakers?
