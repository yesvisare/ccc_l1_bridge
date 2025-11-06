# M1.3 → M1.4 Bridge Validation

Validation notebook for verifying M1.3 document processing pipeline readiness before moving to M1.4 query pipeline.

## Quick Start

### Prerequisites
- Python 3.9+
- Pinecone API key (set `PINECONE_API_KEY` env var)
- OpenAI API key (set `OPENAI_API_KEY` env var)
- Completed M1.3 document processing pipeline

### Run Validation

```bash
# Set environment variables
export PINECONE_API_KEY="your-key-here"
export OPENAI_API_KEY="your-key-here"
export PINECONE_INDEX_NAME="rag-index"  # optional, defaults to "rag-index"

# Install dependencies
pip install pinecone-client openai numpy

# Run notebook
jupyter notebook M1_Bridge_M1.3_to_M1.4.ipynb
```

## Validation Sections

### 1. Recap
- Reviews pipeline architecture (extract → clean → chunk → embed → store)

### 2. Vector Count Check
- **Requirement:** ≥100 vectors in Pinecone
- **Pass:** Vector count meets threshold
- **Fail:** Insufficient vectors → Return to M1.3

### 3. Metadata Validation
- **Requirement:** Vectors have `source`, `chunk_id`, `content_type` fields
- **Pass:** All sampled vectors have required metadata
- **Fail:** Missing metadata keys → Review M1.3 enrichment step

### 4. Chunking Documentation
- **Requirement:** Chunking params documented (strategy, size, overlap)
- **Pass:** Config found or created with defaults
- **Action:** Creates `chunking_params.json` if missing

### 5. Smoke Test
- **Requirement:** Test 3 query types (factual, how-to, comparison)
- **Pass:** All queries return results with scores
- **Output:** Tiny (top score only per query)

### 6. Call-Forward
- **Preview:** M1.4 capabilities (query understanding, reranking, citations)

## Pass/Fail Criteria

### ✅ PASS - Ready for M1.4
- [ ] Pinecone has ≥100 vectors
- [ ] Sample vectors have complete metadata (source, chunk_id, content_type)
- [ ] Chunking strategy documented
- [ ] All 3 smoke test queries return results
- [ ] No API connection errors

### ❌ FAIL - Action Required
- **Fail 1:** Vector count < 100
  - **Action:** Return to M1.3, process more documents

- **Fail 2:** Missing metadata keys
  - **Action:** Review M1.3 metadata enrichment step, add missing fields

- **Fail 3:** Smoke test returns no results
  - **Action:** Verify embeddings stored correctly, check index configuration

### ⚠️ SKIP - Missing Keys
- Missing `PINECONE_API_KEY` or `OPENAI_API_KEY`
  - **Action:** Set environment variables, re-run notebook
  - Notebook gracefully skips API-dependent checks

## Expected Output

Notebook runs incrementally, printing:
```
SAVED_SECTION: 1
SAVED_SECTION: 2
SAVED_SECTION: 3
SAVED_SECTION: 4
SAVED_SECTION: 5
SAVED_SECTION: 6
```

Each section shows:
- ✅ PASS markers for successful checks
- ❌ FAIL markers with remediation guidance
- ⚠️ SKIP markers for gracefully skipped checks
- Tiny outputs (scores only, truncated metadata)

## Files Created

- `M1_Bridge_M1.3_to_M1.4.ipynb` - Validation notebook
- `chunking_params.json` - Default chunking config (if missing)
- `README.md` - This file

## Next Steps

After all checks pass:
1. Proceed to **M1.4 Query Pipeline & Response Generation**
2. Build query understanding, hybrid retrieval, reranking
3. Add response generation with source citations

## Source

- Bridge doc: `bridge_M1_3_to_M1_4.md`
- Repository: https://github.com/yesvisare/ccc_l1_bridge
