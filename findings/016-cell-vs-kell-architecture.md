---
status: resolved
resolution: "Implemented Cell Architecture in konf-genesis.kell and documented Cells vs Kells distinction."
date: 2026-04-10
---

# Finding 016: Cell vs Kell Architecture

## Context
During the deployment of the Genesis Kell, we realized that treating every agent organization as a single repository nested inside the kernel repo was limiting and messy.

## Observation
An organization (a "Cell") needs its own Git lifecycle to track logs, operational "thoughts," and workflow evolutions without polluting the core platform code.

## Resolution
We have formally defined the **Cell Architecture**:
1.  **Kell:** A single computational boundary (agent).
2.  **Cell:** A collection of minimal, composable Kells deployed together (e.g., a standalone repository like `konf-genesis.kell`).
3.  **Reference Kells:** Static templates provided by the platform (`@konf/products/**`).
4.  **Living Cells:** Independent repositories representing actual organisasi state.

## Impact
This promotes the creation of fundamental, minimal, and generic Kells that compose together to produce complex, emergent behavior, while maintaining strict security and Git isolation.
