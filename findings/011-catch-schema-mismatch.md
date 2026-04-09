---
status: open
resolution: "Docs and parser mismatch. Parser expects Vec<CatchBranchSchema>."
experiment: "004"
date: "2026-04-09"
---

# Finding 011: `catch:` schema mismatch

The workflow YAML reference (`docs/product-guide/workflow-reference.md`) documents `catch: error_node` as a string field, but the parser (`schema.rs:53`) defines `catch: Vec<CatchBranchSchema>` which requires an array of objects with `when`, `do`, `with`, `then` fields.

**Status:** OPEN
All existing workflows avoid `catch:` — none in the sandbox use it. The docs and/or parser need to be aligned.

**Impact:** Blocks error-handling workflows (diagnose_failures, safe_fetch patterns).
**Priority:** Medium — needed for experiment 004 completion and robust autonomous agents.
