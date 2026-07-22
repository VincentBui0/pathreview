## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/149

**Issue title:** StructuralChunker returns empty list for documents without markdown headings

**Tier:** [x] Tier 1  [] Tier 2  [ ] Tier 3

**Problem summary:**
`StructuralChunker.chunk()` in `ingestion/chunking/structural_chunker.py` splits documents by markdown headings, but returns an empty list when a document has none. Instead of falling back to treating the whole document as a single chunk, it silently drops it — meaning plain-text documents never make it into the RAG index. The fix is to add a fallback so headingless documents are returned as one chunk rather than zero. There's already a failing test for this case: `test_document_with_no_headings` in `tests/unit/test_structural_chunker.py`.

**Selection notes:**
This is a Tier 1 issue. I chose it because the fix is scoped to a single method in one file, and there's already a failing test pointing directly at the problem — so I can verify my fix without having to write the test from scratch. As someone still getting comfortable with a large codebase, having a clear entry point and a concrete expected behavior made this a good fit.

**Branch name:** feat/149-structural-chunker-fallback

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger

---

## Week 8 — Reproduction & solution planning

**Reproduction commit link:** [link to your reproduction commit]

**Reproduction summary:**
Ran `StructuralChunker().chunk()` against a ~1000-character plain text string with no markdown headings and observed a return value of `[]`. The pre-existing test `test_document_with_no_headings` in `tests/unit/test_structural_chunker.py` also fails as expected.

**PLAN.md link:** [link to PLAN.md in your fork]

**Walkthrough video (recommended):** N/A

**Blockers or open questions:**
Need to confirm whether content appearing before the first heading in a mixed document is currently also silently dropped, and whether the fix should surface that as a separate chunk or leave it out of scope.