## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/149

**Issue title:** StructuralChunker returns empty list for documents without markdown headings

**Tier:** [x] Tier 1  [] Tier 2  [ ] Tier 3

**Problem summary:**
`StructuralChunker.chunk()` in `ingestion/chunking/structural_chunker.py` splits documents by markdown headings, but returns an empty list when a document has none. Instead of falling back to treating the whole document as a single chunk, it silently drops it — meaning plain-text documents never make it into the RAG index. The fix is to add a fallback so headingless documents are returned as one chunk rather than zero. There's already a failing test for this case: `test_document_with_no_headings` in `tests/unit/test_structural_chunker.py`.

**Branch name:** feat/149-structural-chunker-fallback

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger