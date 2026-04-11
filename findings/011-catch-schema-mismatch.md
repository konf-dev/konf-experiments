---
status: resolved
resolution: "Both forms (bare string and conditional branches) are already implemented and now both have test coverage."
experiment: "004"
date: "2026-04-09"
resolved: "2026-04-11"
---

# Finding 011: `catch:` schema mismatch

## Original concern

The workflow YAML reference described `catch: error_node` as a string, while an early parser read only an array of conditional branches. Docs and parser disagreed.

## Actual current state (verified 2026-04-11)

Both forms are implemented and have been for some time. The mismatch is stale.

- **Parser**: `CatchBlock` at `konf/crates/konflux-core/src/parser/schema.rs:136-149` is a `#[serde(untagged)]` enum with two variants: `Simple(String)` for the bare node name form, and `Branches(Vec<CatchBranchSchema>)` for the conditional form.
- **Compiler**: `konf/crates/konflux-core/src/parser/compiler.rs:122-150` matches both arms. `Simple(target)` becomes `ErrorAction::Goto { step: StepId::new(target) }`; `Branches(...)` iterates conditional branches.
- **Validator**: `konf/crates/konflux-core/src/parser/validator.rs:113-141` validates targets in both arms against the node name table.
- **Docs**: `konf/docs/product-guide/workflow-reference.md:80` already documents both forms ("string or array") with examples.
- **Tests**: `konf/crates/konflux-core/tests/integration_tests.rs` now contains `test_error_handling_catch` covering the `Branches` form and `test_error_handling_catch_simple_form` covering the `Simple(String)` form. Both pass.

## Resolution

No code change required. A test was added to lock in the `Simple(String)` form so future refactors can't silently regress it. Status flipped to resolved.
