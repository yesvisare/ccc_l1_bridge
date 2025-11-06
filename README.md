# Bridge M1.4 → M2.1: Cost Baseline Validation

Validation notebook for M1.4 → M2.1 transition, focusing on **cost reality + caching trade-off**.

## Overview

This notebook validates baseline costs before implementing caching in M2.1. It uses **local calculations only** — no live API calls required.

**Source:** `bridge_M1_4_to_M2_1.md`

## Files

- **Bridge_M1.4_to_M2.1_Cost_Baseline.ipynb** — Main validation notebook (6 sections)
- **repeat_rate_assumptions.json** — Editable query repeat rate assumptions
- **README.md** — This file

## Quick Start

### 1. Run the Notebook

```bash
jupyter notebook Bridge_M1.4_to_M2.1_Cost_Baseline.ipynb
```

Execute all cells sequentially to:
1. Review M1 accomplishments
2. Calculate baseline costs ($91/day, $2,730/month at 10K queries/day)
3. Record query repeat-rate assumptions
4. Project caching savings (0%, 30%, 50%, 70% hit rates)
5. Review freshness trade-offs
6. Complete pre-M2.1 readiness checklist

### 2. Edit Assumptions

To test different scenarios, edit `repeat_rate_assumptions.json`:

```json
{
  "semantically_similar_pct": {
    "assumed": 40  ← Change this value
  },
  "exact_near_exact_repeats_pct": {
    "assumed": 20  ← Change this value
  }
}
```

Then re-run **Section 4** (Projected Savings) to see updated projections.

### 3. Adjust Scale

To calculate costs for different query volumes, edit **Section 2** in the notebook:

```python
QUERIES_PER_DAY = 10_000  # Change to your expected volume
```

Then re-run **Section 2** and **Section 4**.

## Reading the Output

### Section 2: Baseline Cost Calculator

**Expected output:**
```
BASELINE COST CALCULATOR
==================================================
Scale: 10,000 queries/day

Daily Costs:
  Embeddings:      $   1.00
  LLM Calls:       $  20.00
  Vector Searches: $  70.00
  ──────────────────────────────
  DAILY TOTAL:     $  91.00

MONTHLY TOTAL:     $ 2,730.00

Per-query cost: $0.0091
```

**How to read:**
- **Embeddings:** OpenAI text-embedding-3-small cost
- **LLM Calls:** GPT generation cost per query
- **Vector Searches:** Pinecone search cost
- **Per-query cost:** Total cost divided by query count

### Section 4: Caching What-If Table

**Expected output:**
```
CACHING WHAT-IF TABLE
======================================================================
Cache Hit Rate       Daily Cost      Monthly Cost    Savings %
──────────────────────────────────────────────────────────────────────
0% (baseline)        $91.00          $2,730.00       -
30%                  $63.70          $1,911.00       30.0%
50%                  $45.50          $1,365.00       50.0%
70%                  $27.30          $819.00         70.0%
```

**How to read:**
- **0% (baseline):** No caching; current state
- **30% cache hit rate:** 30% of queries served from cache → save ~$819/month
- **50% cache hit rate:** 50% of queries served from cache → save ~$1,365/month
- **70% cache hit rate:** 70% of queries served from cache → save ~$2,091/month

**Key insight:** Cache hit rate directly correlates with cost savings. Higher repeat query rates → higher hit rates → more savings.

## Customization Guide

### Change Cost Constants

Edit **Section 2** in the notebook:

```python
COST_EMBEDDING_PER_QUERY = 0.0001    # OpenAI embedding cost
COST_LLM_PER_QUERY = 0.002            # LLM generation cost
COST_VECTOR_SEARCH_PER_QUERY = 0.007  # Pinecone search cost
```

Update these values if:
- You use different models (e.g., text-embedding-3-large)
- Pricing changes
- You negotiate volume discounts

### Test Different Cache Scenarios

Edit **Section 4** in the notebook:

```python
cache_scenarios = [0, 30, 50, 70]  # Add/remove hit rates
```

Example: Test aggressive caching strategy:
```python
cache_scenarios = [0, 40, 60, 80, 90]
```

### Adjust Repeat Rate Assumptions

Based on your actual query logs, update `repeat_rate_assumptions.json`:

**Conservative scenario** (fewer repeats):
```json
{
  "semantically_similar_pct": {"assumed": 30},
  "exact_near_exact_repeats_pct": {"assumed": 15}
}
```

**Optimistic scenario** (more repeats):
```json
{
  "semantically_similar_pct": {"assumed": 50},
  "exact_near_exact_repeats_pct": {"assumed": 25}
}
```

## Decision Framework

Use **Section 5** to decide if caching is appropriate for your use case:

### ✓ Caching Makes Sense When:
- Stable documentation (API docs, how-to guides)
- Historical data (archives, past reports)
- FAQ-style content (policies, procedures)
- Acceptable staleness window (hourly/daily updates OK)
- High query repetition rate (>30% similar queries)

### ❌ Skip Caching When:
- Knowledge base updates every 5 minutes
- Real-time data requirements (stock prices, live metrics)
- Rapidly changing content (news, social feeds)
- Compliance requires latest data (regulatory, legal)
- Query repetition rate low (<20% similar queries)

## Pre-M2.1 Checklist

Before proceeding to M2.1, ensure:

- [ ] Baseline metrics confirmed ($91/day at 10K queries/day)
- [ ] Repeat rate assumptions documented (30-50% similar queries)
- [ ] Target cache hit rate identified (30%, 50%, or 70%)
- [ ] Staleness window defined (hours? days?)
- [ ] Cost vs freshness priority clarified
- [ ] M1.4 query pipeline working reliably

## Troubleshooting

**Q: Section 3 doesn't create `repeat_rate_assumptions.json`**
A: Ensure you have write permissions in the current directory. Run `pwd` to verify location.

**Q: Calculations don't match bridge document values**
A: Verify `QUERIES_PER_DAY = 10_000` in Section 2. Bridge document uses 10K queries/day baseline.

**Q: What if my repeat rate is unknown?**
A: Use bridge document defaults (40% similar, 20% exact repeats). Update after analyzing your query logs in M2.1.

**Q: Should I cache if my hit rate will be low (<30%)?**
A: Probably not worth the complexity. Consider **M2.2 Prompt Optimization** instead for cost reduction.

## Next Steps

After completing this notebook:

1. **Review all sections** — Ensure you understand cost baseline and trade-offs
2. **Complete Section 6 checklist** — Verify readiness for M2.1
3. **Proceed to M2.1** — Caching Strategies for Cost Reduction (38 min video + 60-90 min hands-on)

## Source Document

All data and calculations based on:
**bridge_M1_4_to_M2_1.md** — Bridge script from M1.4 Query Pipeline to M2.1 Caching Strategies

## Support

Issues or questions? Review:
- **Section 5:** Risks & Trade-offs
- **Section 6:** Pre-M2.1 Checklist
- **bridge_M1_4_to_M2_1.md:** Original source material
