---
status: verified
resolution: "Was a config issue (missing `register_as_tool: true`), not a code bug."
experiment: "001"
date: "2026-04-09"
---
# Finding 009: Nested workflow tools not available in child scope

**Experiment:** 001
**Date:** 2026-04-09
**Severity:** High — blocks workflow composition

## What happened

A `meta_fetch` workflow calling another workflow (`workflow:fetch_and_extract`) failed with: `tool not found: workflow:fetch_and_extract`.

## Root cause

The investigation revealed this was not a bug in the runtime's tool copying mechanism as initially suspected. The `fetch_and_extract` workflow was missing `register_as_tool: true` in its YAML header. Without this flag, the engine does not register the workflow in the tool registry, so it's not available to be called from other workflows.

## Fix

Added `register_as_tool: true` to `fetch_and_extract.yaml`. Nested composition now works as expected.

## Lesson

The system worked correctly, but the configuration requirement was not immediately obvious. The error message `tool not found` was accurate but could be improved to suggest checking `register_as_tool`.
