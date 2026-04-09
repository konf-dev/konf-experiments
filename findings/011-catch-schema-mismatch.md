---
status: fixed
resolution: "`CatchBlock` is now an untagged enum accepting a string or array of branches."
experiment: "004"
date: "2026-04-09"
---
# Finding 011: `catch:` schema mismatch

The workflow YAML reference (`docs/product-guide/workflow-reference.md`) documents `catch: error_node` as a string field, but the parser (`schema.rs:53`) defined `catch: Vec<CatchBranchSchema>` which required an array of objects with `when`, `do`, `with`, `then` fields. All existing workflows avoided `catch:` — none in the sandbox used it. The docs and/or parser needed to be aligned.

**Impact:** Blocks error-handling workflows (diagnose_failures, safe_fetch patterns).
**Priority:** Medium — needed for experiment 004 completion and robust autonomous agents.
