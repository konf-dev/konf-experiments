---
status: resolved
resolution: "Runner shipped as a tool family in `konf-tool-runner`, not a kernel trait."
experiment: "phase-3-runner"
date: "2026-04-11"
resolved: "2026-04-11"
---

# Finding 017: The runner is a tool family, not a kernel primitive

## Question

When a konf workflow needs to **start another workflow as a long-running,
independently observable run** — with `spawn`, `status`, `wait`, and
`cancel` — where should that primitive live?

Two candidates:

1. A `Runner` trait in `konf-runtime`, called by the engine during workflow
   dispatch. This would grow the kernel surface.
2. A dedicated tool crate (`konf-tool-runner`) that implements the Runner
   trait *internally*, and exposes the behavior as regular tools
   (`runner:spawn`, `runner:status`, `runner:wait`, `runner:cancel`).

## Answer

Tool family, not kernel primitive.

Finding 014 said the only new kernel primitive required for autonomous
agents was the `schedule` tool — and schedule is itself a tool, not a
trait in `konf-runtime`. The runner is the same shape at a different axis:
schedule handles "when"; the runner handles "how isolated / where". Both
can be expressed through the existing `Tool` trait over the existing
`Engine`, without growing the kernel.

The `Runner` trait still exists — but it lives **inside** the
`konf-tool-runner` crate (`crates/konf-tool-runner/src/runner.rs`), not
in `konf-runtime`. Callers never see the trait directly; they see the
four registered tools. Tool annotations (read-only, destructive, etc.)
carry the right semantics automatically, capability attenuation works
without ceremony, and future isolation backends (systemd, docker) can
land as independent impls of the same trait.

## What shipped (v1)

- `konf-tool-runner` crate with `Runner` trait, shared `RunRegistry`,
  and `InlineRunner` backend.
- Four tools registered during `konf-init::boot` step 9d:
  `runner:spawn`, `runner:status`, `runner:wait`, `runner:cancel`.
- `products/ci/` product demonstrating the fan-out pattern via a `gate`
  workflow.
- 10 integration tests covering happy path, failing workflow, unknown
  workflow, cancel-in-flight, parallel fan-out, tool-surface wiring,
  and error paths.

## What is explicitly deferred

- **`SystemdRunner`** (Linux transient-unit isolation) — one crate commit,
  no trait change.
- **`DockerRunner`** (cross-platform container isolation via `bollard`) —
  same shape, one crate commit.
- **`runner:logs`** (streaming stdout/stderr) — v2; today the workflow's
  return value carries any captured output.
- **`konf-mcp --run-spec`** (out-of-process workflow execution) — only
  needed once a non-inline runner lands.

None of these require a kernel change or a trait change. They are all
new backends that plug into the existing registry and the existing
four tool shells.

## Cross-references

- `konf-experiments/findings/014-only-one-new-rust-tool.md` — the
  principle this finding extends.
- `konf/docs/MENTAL_MODEL.md` — runner tools documented in the
  tool vocabulary.
- `konf/crates/konf-tool-runner/src/` — implementation.
- `konf/products/ci/README.md` — the fan-out demo.
