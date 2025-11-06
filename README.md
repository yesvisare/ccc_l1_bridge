# Bridge M2.1 → M2.2: Readiness Validation

This repository contains a validation notebook to ensure you've completed M2.1 (Caching Strategies) before proceeding to M2.2 (Prompt Optimization).

## Quick Start

```bash
jupyter notebook Bridge_M2.1_to_M2.2_Readiness.ipynb
```

Run all cells to validate your readiness for M2.2.

---

## How to Pass Each Check

### Check #1: Cache Hit Rate (≥30%)

**What it checks:** Redis cache operational with minimum 30% hit rate

**Three options to provide data:**

1. **Live Redis (if available):**
   - Notebook will auto-run: `redis-cli INFO stats`
   - Calculates: `keyspace_hits / (keyspace_hits + keyspace_misses)`

2. **Manual input:**
   - When prompted, enter your hit rate as decimal (e.g., `0.42` for 42%)

3. **Stub file (default):**
   - Edit `cache_metrics_stub.json`:
     ```json
     {
       "cache_hit_rate": 0.40,
       "note": "Replace with actual metrics"
     }
     ```

**Pass criteria:**
- Hit rate ≥ 0.30 (30%)
- If <30%: Warning to return to M2.1 [14:30] for semantic cache tuning

---

### Check #2: Cost Savings Baseline (30-70%)

**What it checks:** Analytics file showing `cost_savings_vs_baseline` field exists

**How to pass:**

Edit `cost_analytics_stub.json`:
```json
{
  "baseline_cost_per_day": 21.00,
  "current_cost_per_day": 12.60,
  "cost_savings_vs_baseline": 0.40,
  "note": "Replace with actual analytics data"
}
```

**Required field:** `cost_savings_vs_baseline` (decimal, e.g., 0.40 = 40%)

**Pass criteria:**
- Field exists in JSON
- Typical range: 0.30-0.70 (30-70% savings)

**Where to get real data:**
- Your caching analytics dashboard
- Cost monitoring script output
- Production metrics from M2.1

---

### Check #3: Failures Documented (5/5)

**What it checks:** All 5 common cache failures documented with fixes

**How to pass:**

The notebook searches for:
- `failures_documentation.md` (preferred)
- `README.md` (if contains failure docs)
- `FAILURES.md`

**Option 1: Fill the template**

Run the notebook once to generate `failures_documentation.md`, then fill:

```markdown
# Cache Failures & Fixes

## 1. Cache Stampede on Cold Start
**Problem:** Multiple requests hit DB simultaneously when cache empty
**Fix:** Implemented request coalescing with mutex lock

## 2. Stale Data After Updates
**Problem:** Cache returned old data after DB update
**Fix:** Event-based invalidation on write operations

## 3. Redis Memory Overflow (OOM)
**Problem:** Redis crashed when cache exceeded 2GB
**Fix:** Set maxmemory policy to LRU eviction

## 4. Hash Collisions
**Problem:** Different queries mapped to same cache key
**Fix:** Used SHA-256 instead of simple hash

## 5. Connection Timeouts
**Problem:** Requests failed when Redis latency >500ms
**Fix:** Added connection pooling + retry logic
```

**Option 2: Document in existing README.md**

Add a "## Cache Failures" section with all 5 failures.

**Pass criteria:**
- File exists with all 5 failure keywords:
  - `cache stampede`
  - `stale data`
  - `memory overflow`
  - `hash collision`
  - `connection timeout`

---

### Check #4: Query Diversity Metric

**What it checks:** CSV or JSON file containing query diversity analysis

**How to pass:**

Edit `query_diversity.json`:
```json
{
  "diversity_score": 0.45,
  "total_queries": 1000,
  "unique_patterns": 450,
  "similarity_distribution": {
    "0-30%": 0.20,
    "30-70%": 0.50,
    "70-100%": 0.30
  },
  "note": "Replace with actual query diversity analysis"
}
```

**Alternative: CSV format**

Create `query_diversity.csv`:
```csv
similarity_range,percentage
0-30,0.20
30-70,0.50
70-100,0.30
```

**Where to get real data:**
- Query log analysis from M2.1
- Semantic similarity clustering
- RAG analytics dashboard

**Why it matters:**
- High diversity (>70%): Focus on prompt compression in M2.2
- Low diversity (<30%): Focus on semantic cache tuning
- Medium (30-70%): Balanced approach

**Pass criteria:**
- File exists (JSON or CSV)
- Contains diversity score or distribution

---

## Check #5: Call-Forward (Info Only)

This section previews M2.2 capabilities. No action required—just read to understand what's next:

1. **RAG-Specific Prompt Engineering** (30-50% token reduction)
2. **Intelligent Model Routing** (simple→cheap, complex→premium)
3. **Token Optimization Techniques** (truncation, compression)

**Expected outcome:** 50-70% total cost reduction (caching + optimization)

---

## Complete Validation Checklist

Before proceeding to M2.2, ensure:

- [ ] `cache_metrics_stub.json` created with hit rate ≥30%
- [ ] `cost_analytics_stub.json` has `cost_savings_vs_baseline` field
- [ ] `failures_documentation.md` documents all 5 failures
- [ ] `query_diversity.json` contains diversity metrics
- [ ] All notebook cells run without errors

---

## File Structure

```
.
├── Bridge_M2.1_to_M2.2_Readiness.ipynb  # Main validation notebook
├── README.md                            # This file
├── bridge_M2_1_to_M2_2.md              # Bridge script (reference)
│
├── cache_metrics_stub.json             # Check #1: Cache hit rate
├── cost_analytics_stub.json            # Check #2: Cost savings
├── failures_documentation.md           # Check #3: Failures docs
└── query_diversity.json                # Check #4: Diversity metrics
```

---

## Troubleshooting

**"Redis not available" error**
- Normal if Redis not running locally
- Use manual input or stub file instead

**"No documentation found"**
- Run notebook once to generate templates
- Fill templates with your M2.1 work

**"Diversity score undefined"**
- Run notebook to create stub
- Replace with actual query analysis from M2.1

**All checks fail**
- This is expected on first run
- Notebook creates stub files automatically
- Fill stubs with your actual M2.1 data
- Re-run notebook to validate

---

## Next Steps

1. Run the validation notebook
2. Fill all stub files with your M2.1 data
3. Re-run until all checks pass
4. Proceed to **M2.2: Prompt Optimization & Model Selection**

**Estimated time:** 5-10 minutes to validate (if M2.1 complete)

---

## Resources

- **Bridge Script:** `bridge_M2_1_to_M2_2.md`
- **Previous Module:** M2.1 Caching Strategies for Cost Reduction (Augmented)
- **Next Module:** M2.2 Prompt Optimization & Model Selection (Augmented)

---

**Ready for M2.2?** Run the notebook and achieve ✓ on all checks!
