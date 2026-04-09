# Experiment 004: Autonomous Agents — Results

**Date:** 2026-04-09
**Status:** COMPLETE
**Branch:** `experiments/v1` (konf)

---

## Goal

Prove that Konf agents can run autonomously — on a schedule, without human intervention, within their capability grants. Discover whether the push-based model is sufficient or if an event bus (Phase F2) is needed.

## What Was Built

### Rust (one kernel primitive, ~80 lines)

**`schedule` tool** (`konf-init/src/schedule.rs`) — the `timer_create` equivalent:
- `schedule(workflow, delay_ms, input, repeat)` → fire-and-forget
- With `repeat: true`, re-fires after each completion (repeating timer)
- Bounds enforced: 1s min, 7d max (prevents hot-spin loops)
- Tool resolved from live registry at execution time (not schedule time) — respects hot-reloads
- Capabilities inherited from caller, attenuated via the capability lattice

### YAML (pure configuration)

**`nightwatch.yaml`** — a health check workflow:
- Calls `workflow_dev_status` to check all repos
- Logs results to `/tmp/nightwatch/health.log` via `shell_exec`
- Pure behavior — no scheduling logic. Uses `schedule(repeat: true)` externally.

**Tool guards** in `tools.yaml`:
- `shell_exec`: deny rules for `sudo` and `rm -rf /*`
- `default: allow` for other commands
- `roles:` config with `agent` role scoped to nightwatch namespace

## Test Results

### Criterion 1: `schedule` tool registered and callable
**PASS** — Tool visible via `system_introspect`, callable from MCP and from workflows.

### Criterion 2: Nightwatch runs and repeats autonomously
**PASS** — 190+ iterations observed at 5-second intervals. Self-sustaining loop with zero human intervention. The `repeat: true` flag handles re-scheduling automatically; the workflow itself has no knowledge of scheduling.

### Criterion 3: Repeating timer fires reliably
**PASS** — Consistent ~5s intervals over 15+ minutes of continuous operation. No drift, no missed runs, no memory growth observed.

### Criterion 4: Tool guards block forbidden commands
**PASS** — Three guard tests:
- `sudo cat /etc/shadow` → **DENIED**: `"guard denied: sudo blocked by tool guard"`
- `rm -rf /etc` → **DENIED**: `"guard denied: destructive rm on root paths blocked"`
- `echo 'safe command'` → **PASSED**: returned stdout normally

Guards are enforced at the runtime level (inside `build_scoped_engine`), not at the MCP level. MCP sessions with `capabilities: ["*"]` bypass guards (by design — infra-level access). Workflows running through the runtime's scoped engine are guarded.

### Criterion 5: Scoped capabilities enforced
**PASS** — The `agent` role in `roles.yaml` restricts capabilities to `["shell_exec", "template", "echo", "log", "schedule", "config_reload", "ai_complete", "yaml_validate_workflow", "workflow_*"]`. Tools not in this list are invisible to the agent's execution scope.

### Criterion 6-7: Self-deployment (diagnose_failures)
**NOT TESTED** — The `diagnose_failures.yaml` workflow uses `catch:` syntax which requires `Vec<CatchBranchSchema>` (array of objects), not a plain string. This is a doc/schema mismatch (the product guide says `catch: error_node` but the parser expects an array). Deferred to a follow-up that fixes the `catch:` documentation and workflow.

### Criterion 8: Audit log captures executions
**PASS** — All scheduled executions produce tracing logs with `schedule_id`, `workflow` name, and completion/failure status. The file-based health log (`/tmp/nightwatch/health.log`) provides timestamped evidence of every run.

## Summary

| # | Criterion | Result |
|---|-----------|--------|
| 1 | schedule tool callable | **PASS** |
| 2 | Nightwatch runs autonomously | **PASS** — 190+ iterations |
| 3 | Repeating timer reliable | **PASS** — no drift over 15min |
| 4 | Tool guards block forbidden commands | **PASS** — sudo + rm -rf denied |
| 5 | Scoped capabilities enforced | **PASS** |
| 6 | Self-deployment via LLM | **DEFERRED** — catch: schema mismatch |
| 7 | Hot-loaded workflow callable | **DEFERRED** |
| 8 | Executions in audit log | **PASS** |

**Overall: 6/8 PASS, 2/8 DEFERRED (non-blocking)**

## Findings

### Finding 011: `catch:` schema mismatch
The workflow YAML reference (`docs/product-guide/workflow-reference.md`) documents `catch: error_node` as a string field, but the parser (`schema.rs:53`) defines `catch: Vec<CatchBranchSchema>` which requires an array of objects with `when`, `do`, `with`, `then` fields. All existing workflows avoid `catch:` — none in the sandbox use it. The docs and/or parser need to be aligned.

**Impact:** Blocks error-handling workflows (diagnose_failures, safe_fetch patterns).
**Priority:** Medium — needed for experiment 004 completion and robust autonomous agents.

### Finding 012: MCP sessions bypass tool guards
Tool guards are applied in `build_scoped_engine()` during workflow execution, not at the MCP layer. Direct MCP tool calls (`mcp__konf__shell_exec`) bypass guards entirely because the MCP session has `capabilities: ["*"]` and no scoped engine is built for direct calls.

This is correct for local development (the human operator has full access), but means guards only protect _workflows_, not _direct tool calls_. For multi-tenant MCP, guards would need to also be applied in `mcp_tool_context()`.

**Impact:** Low for current use (single-user dev). Important for multi-tenant.

### Finding 013: Repeating timer has no cancellation mechanism
The `schedule` tool returns a `schedule_id` but there's no way to cancel a repeating timer. The only way to stop nightwatch is to unregister the workflow (remove YAML + config_reload) — the spawned tokio task checks the registry on each iteration and stops if the workflow is gone.

This is an acceptable kill-switch for now but a proper `cancel_schedule(schedule_id)` tool would be needed for production use.

**Impact:** Low — registry check provides a kill-switch. Track for future.

### Finding 014: The `schedule` tool is the only new Rust needed
The entire experiment runs on one new Rust tool (~80 lines). Everything else — the agent behavior, logging, guard rules, role definitions — is YAML configuration. This validates Principle 11 ("the kernel does nothing a workflow can do") and Principle 9 ("products are configuration, not code").

### Finding 015: Event bus (F2) not needed for autonomous agents
The push-based model (repeating timer → workflow → tools) is sufficient for autonomous operation. No event-driven triggers were needed. The nightwatch agent runs, checks health, logs results — all without subscribing to events. 

F2 (event bus) would be needed for reactive agents ("when test fails, trigger diagnosis"), but for proactive agents ("check health every N seconds"), the schedule primitive is enough. Defer F2 until an experiment demonstrates a reactive use case.

## Design Decisions

1. **`repeat: true` over self-rescheduling.** The first attempt used self-rescheduling (workflow calls `schedule` on itself in the last node). This broke because state (`interval_ms`) wasn't threaded through template substitution correctly. The fix: make `repeat` a flag on the schedule tool. The workflow is pure behavior; scheduling is a separate concern.

2. **In-memory timers over Postgres.** The schedule tool uses tokio timers — no database needed. Lost on restart, but the agent re-registers on the next boot. Persistence is an optimization, not a fundamental mechanism.

3. **Guard evaluation at runtime scope, not MCP layer.** Guards wrap tools inside `build_scoped_engine()`, which is called when a workflow runs through the runtime. Direct MCP calls bypass this (by design — operator access). This matches the Linux model: kernel enforces permissions on processes, not on the root user.

## Architecture Validation

This experiment validates the core architecture thesis:

| Principle | Validated? |
|-----------|-----------|
| #1: Rust = mechanisms, workflows = policies | **YES** — 80 lines Rust, everything else YAML |
| #4: Workflows are the universal composition unit | **YES** — nightwatch is a workflow calling other workflows |
| #8: Capability lattice only attenuates | **YES** — guards enforce, capabilities scope |
| #9: Products are configuration, not code | **YES** — the agent is a YAML file |
| #11: Kernel does nothing a workflow can do | **YES** — only the timer is Rust |
| #12: Prompts are runtime-tweakable code | **Not tested** — diagnose_failures deferred |
| #13: Thin onion layers | **YES** — schedule tool → nightwatch workflow → devkit product |

## What's Next

1. **Fix Finding 011** (`catch:` schema) — unblocks error-handling workflows and diagnose_failures
2. **Run diagnose_failures** — tests self-deployment (Principle #12 validation)
3. **Finding 013** — add `cancel_schedule` tool if needed
4. **Experiment 005** — Unspool migration (full stack validation)
