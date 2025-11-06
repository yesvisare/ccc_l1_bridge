# M1.1 → M1.2 Bridge — Readiness Checks & Call-Forward

## Purpose

This minimal validation notebook confirms your M1.1 setup is ready for advancing to **M1.2: Pinecone Data Model & Advanced Indexing**. It verifies 4 critical prerequisites before introducing hybrid search, namespaces, and performance optimization.

**This is NOT an augmented lab.** It contains only readiness checks and a preview of what's ahead.

## Prerequisites

1. **Completed M1.1** - Understanding Vector Databases
2. **Python 3.8+** with pip
3. **API Access:**
   - OpenAI API key (embeddings)
   - Pinecone API key (vector database)
   - Existing Pinecone index (1536-dim, cosine metric)

## Setup

1. **Clone and navigate:**
   ```bash
   git clone https://github.com/yesvisare/ccc_l1_bridge.git
   cd ccc_l1_bridge
   ```

2. **Install dependencies:**
   ```bash
   pip install pinecone-client openai python-dotenv
   ```

3. **Configure environment:**
   ```bash
   cp .env.example .env
   # Edit .env with your actual API keys and index name
   ```

## Running the Notebook

Launch Jupyter and open `M1_Bridge_M1.1_to_M1.2_Validation.ipynb`:

```bash
jupyter notebook M1_Bridge_M1.1_to_M1.2_Validation.ipynb
```

**Execute each cell sequentially.** The notebook is designed to:
- Gracefully skip checks if API keys are missing (prints `⚠️ Skipping (no keys)`)
- Print compact outputs (≤5 lines per cell)
- Save your baseline threshold to `bridge_baseline.json`

## What Counts as "Pass"?

| Section | Pass Criteria |
|---------|---------------|
| **1. Recap & Why Next** | Read and understand the dense-only gap |
| **2. Check 1 — Pinecone Index** | ✅ Prints dimension = 1536 |
| **3. Check 2 — OpenAI Embedding** | ✅ Prints 1536 dimensions created |
| **4. Check 3 — Metadata** | ✅ Prints metadata keys (or "No vectors yet" is acceptable) |
| **5. Check 4 — Baseline Threshold** | ✅ Creates `bridge_baseline.json` with your threshold |
| **6. Next Up Preview** | Read and understand M1.2 capabilities |

**If any check fails with ❌ Error:** Review your M1.1 setup before proceeding to M1.2.

## Files Generated

- `bridge_baseline.json` - Your recorded similarity threshold for M1.2 comparison

## Next Steps

**All checks passed?** → Proceed to **M1.2: Pinecone Data Model & Advanced Indexing**

**In M1.2 you'll build:**
- Hybrid Search (dense + sparse vectors)
- Advanced namespaces (multi-tenant isolation)
- Dynamic alpha tuning (query-specific optimization)
- Reranking (quality improvements)
- Performance optimization (batching, pooling)

## Troubleshooting

**"⚠️ Skipping (no keys)"**
- Verify `.env` file exists and contains valid API keys
- Restart Jupyter kernel after updating `.env`

**"❌ Error: 404"**
- Confirm `PINECONE_INDEX` name matches your existing index
- Check `PINECONE_REGION` is correct

**"Dimension mismatch"**
- Verify your index was created with 1536 dimensions
- Use `text-embedding-3-small` (1536-dim) or `text-embedding-ada-002` (1536-dim)

## Repository Structure

```
ccc_l1_bridge/
├── M1_Bridge_M1.1_to_M1.2_Validation.ipynb  # Main validation notebook
├── .env.example                              # Template for API keys
├── README.md                                 # This file
└── bridge_M1_1_to_M1_2.md                   # Video script reference
```

## License

Educational use only. Part of the Claude Code Challenge curriculum.

---

**Questions?** Review the bridge video script in `bridge_M1_1_to_M1_2.md` for context on the dense-only gap and M1.2 preview.
