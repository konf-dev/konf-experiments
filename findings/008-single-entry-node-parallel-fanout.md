---
status: by-design
resolution: "Single entry node is by design. Parallelism via `then: [a, b]` fan-out."
experiment: "001"
date: "2026-04-09"
---
# Finding 008: Single entry node required — parallel via fan-out

**Experiment:** 001
**Date:** 2026-04-09
**Severity:** Low — design choice, not a bug

## What happened

A workflow with two independent start nodes was rejected by the parser as having an "orphaned node".

## Root cause

The parser uses the first YAML node as the single entry point. All other nodes must be reachable from that entry.

## How parallel execution works

Use a start node with `then: [a, b]` to fan out. Both `a` and `b` will run concurrently. A subsequent node that depends on both (`then: combine` from both `a` and `b`) acts as an implicit join, waiting for both branches to complete.

## Observation

This is a reasonable design — a single entry ensures the DAG is always a connected graph. The fan-out pattern is clean and explicit.
