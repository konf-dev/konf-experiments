# Finding 009: Nested workflow tools not available in child scope

**Experiment:** 001
**Date:** 2026-04-09
**Severity:** High — blocks workflow composition

## What happened

`meta_fetch` workflow calls `workflow_fetch_and_extract` as a tool. Fails with: `tool not found: workflow_fetch_and_extract`.

## Root cause

When WorkflowTool executes a workflow, the runtime creates a new child Engine and copies tools from the parent based on capability grants (`runtime.rs` lines 160-181). The `workflow_*` tools exist in the parent engine, but they may not be copied into the child engine correctly.

Possible causes:
1. WorkflowTool holds a reference to the runtime — copying it would create circular references
2. The tool copy loop may run before workflow tools are registered
3. The child engine may be a fresh Engine without the workflow tool registry

## Impact

Workflow-calling-workflow (recursive composition) doesn't work. This blocks the most powerful composition primitive — workflows as reusable building blocks.

## Investigation needed

Check `runtime.rs` lines 160-181 to see exactly how tools are copied to the child scope. Are WorkflowTool instances included? If not, why?
