# Bridge M1.2 → M1.3 Validation Notebook

## Purpose

This validation notebook verifies your M1.2 hybrid search implementation and confirms readiness for M1.3 document processing pipeline.

**Bridge Connection:** M1.2 (Pinecone Hybrid Search) → M1.3 (Document Processing Pipeline)

---

## How to Run

### Prerequisites

```bash
# Optional: Set environment variables for full validation
export PINECONE_API_KEY="your-api-key"
export OPENAI_API_KEY="your-api-key"
```

### Execute Notebook

1. Open `M1_Bridge_M1.2_to_M1.3.ipynb` in Jupyter Lab/Notebook
2. Run all cells sequentially
3. Review validation results for each section

### Without API Keys

The notebook gracefully handles missing API keys by printing:
```
⚠️ Skipping (no keys) - Set PINECONE_API_KEY to validate
```

Conceptual validations will still run (namespaces, metadata size checks).

---

## Notebook Sections

1. **Recap:** M1.2 hybrid search achievements (sparse-dense vectors, namespaces, alpha tuning)
2. **Check 1:** BM25 encoder fitted and saved to disk
3. **Check 2:** Index metric configured as `dotproduct`
4. **Check 3:** Namespace isolation prevents data leakage
5. **Check 4:** Metadata size stays under 40KB limit
6. **Call-Forward:** Why M1.3 automated document processing matters

---

## Pass Criteria

### Green Flags (Ready for M1.3)

- ✓ All checks pass or gracefully skip with "⚠️ Skipping (no keys)"
- ✓ Conceptual validations confirm understanding of:
  - Hybrid search architecture
  - Namespace isolation strategy
  - Metadata size constraints

### Red Flags (Review M1.2 First)

- ✗ Index metric is NOT `dotproduct` (hybrid search will fail)
- ✗ Metadata size exceeds 40KB (silent upsert failures)
- ✗ Missing understanding of namespace isolation (data leakage risk)

---

## Expected Output

**Successful validation:**
```
M1.2 Achievements Validated:
  ✓ Hybrid search (sparse + dense vectors)
  ✓ Namespace isolation for multi-tenancy
  ✓ Alpha tuning for semantic/keyword balance

⚠️ No BM25 file found (stub ok - will be created in M1.3)
⚠️ Skipping (no keys) - Set PINECONE_API_KEY to validate

✓ Namespace isolation concept validated
✓ Metadata size validation: PASS
```

---

## Next Steps

After completing validation:

1. ✓ Confirm all checks passed or gracefully skipped
2. → Proceed to **M1.3: Document Processing Pipeline**
3. Learn to build automated extract → clean → chunk → enrich pipeline

**M1.3 Preview:**
- Automated document extraction (PDF, DOCX, Markdown)
- Intelligent chunking strategies (semantic, paragraph-aware)
- Metadata enrichment for better search results

---

## Troubleshooting

**Issue:** Index metric is not `dotproduct`
- **Fix:** Recreate Pinecone index with `metric="dotproduct"`
- **Impact:** Sparse vectors require dotproduct metric

**Issue:** BM25 file not found
- **Status:** OK - This is expected if you haven't persisted the encoder yet
- **Note:** M1.3 will cover document ingestion where BM25 fitting becomes critical

**Issue:** Cannot connect to Pinecone
- **Fix:** Set `PINECONE_API_KEY` environment variable
- **Alternative:** Review conceptual validations only (sufficient for bridge understanding)

---

## Files

- `M1_Bridge_M1.2_to_M1.3.ipynb` - Main validation notebook
- `README.md` - This file
- `bridge_M1_2_to_M1_3.md` - Bridge specification (video script)

---

**Estimated Time:** 10-15 minutes to run and validate

**Source:** CCC L1 Bridge - M1.2 → M1.3
