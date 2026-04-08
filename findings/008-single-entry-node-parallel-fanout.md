# Finding 008: Single entry node required — parallel via fan-out

**Experiment:** 001
**Date:** 2026-04-09
**Severity:** Low — design choice, not a bug

## What happened

Tried creating a workflow with two independent start nodes (`fetch_a`, `fetch_b`). Parser rejected it: "orphaned node 'fetch_b' — not reachable from entry."

## Root cause

The parser uses the first YAML node as the single entry point (`compiler.rs` line 18-19). Reachability is checked from that entry via DFS. Nodes not reachable from the entry are rejected as orphans.

## How parallel execution works

Use a start node with `then: [a, b]` to fan out:
```yaml
nodes:
  start:
    do: echo
    then: [fetch_a, fetch_b]
  fetch_a: ...
  fetch_b: ...
  combine:
    ...
```

Both `fetch_a` and `fetch_b` run concurrently (verified by trace — completed within 1ms of each other at 596ms and 597ms). The `combine` node waits for both via implicit join (both have `then: combine`).

## Observation

This is a reasonable design — single entry ensures the DAG is always a connected graph. The fan-out pattern via `then: [a, b]` is clean and explicit.
