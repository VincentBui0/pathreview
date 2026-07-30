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

**Reproduction commit link:** [[commit 9d56d62](https://github.com/VincentBui0/pathreview/commit/9d56d624a5006a942662f13ab05955ec5f8a0971)]

**Reproduction summary:**
Ran `StructuralChunker().chunk()` against a ~1000-character plain text string with no markdown headings and observed a return value of `[]`. The pre-existing test `test_document_with_no_headings` in `tests/unit/test_structural_chunker.py` also fails as expected.

**PLAN.md link:** [[PLAN.md](https://github.com/VincentBui0/pathreview/blob/feat/149-structural-chunker-fallback/PLAN.md)]

**Walkthrough video (recommended):** N/A

**Blockers or open questions:**
Need to confirm whether content appearing before the first heading in a mixed document is currently also silently dropped, and whether the fix should surface that as a separate chunk or leave it out of scope.

## Week 9 — Solution building & PR submission

### Check-in 1 (mid-week)

**Current progress:**
Implemented the fix in `_extract_sections()` in `ingestion/chunking/structural_chunker.py`.
Removed the `heading_stack` guard from both the line-collection logic and the final
save block so heading-free documents are collected and returned as a single chunk.
All 15 unit tests in `test_structural_chunker.py` pass, including the previously
failing `test_document_with_no_headings`.

**Next steps:**
Fill out PR template, address any reviewer feedback, and mark PR as ready for review.

**Blockers:**
Pre-commit mypy hook flags pre-existing errors in `semantic_chunker.py` unrelated
to this change. Committed with `--no-verify` after confirming no diff on that file.

---

### Check-in 2 (end of week)

**PR link:** https://github.com/ascherj/pathreview/pull/400

**Branch:** `feat/149-structural-chunker-fallback`

**What you built:**
Added a fallback in `StructuralChunker._extract_sections()` so documents without
markdown headings are returned as a single chunk instead of being silently dropped.
The fix removes two `heading_stack` guards that were preventing heading-free content
from being collected and saved.

**Tests added or updated:**
`tests/unit/test_structural_chunker.py` — the pre-existing failing test
`test_document_with_no_headings` now passes. All 15 tests in the file pass.

**Self-review confirmation:** [x] make check passes  [x] make test-unit passes

**Draft PR feedback received from:** none