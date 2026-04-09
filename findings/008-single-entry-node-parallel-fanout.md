---
status: by-design
resolution: "Entry node starts the flow; 'then' handles fan-out."
experiment: "001"
date: "2026-04-09"
---

# Finding 008: Single entry node / fan-out

A common workflow pattern involves multiple starting points or broad parallel execution. Initially, it wasn't clear how to represent this with a single YAML-defined entry point.

**Status:** BY-DESIGN
Konf uses a single entry node (the first one defined in the YAML file). To achieve parallel fan-out, the entry node should list multiple nodes in its `then:` field (e.g., `then: [node_a, node_b]`).
