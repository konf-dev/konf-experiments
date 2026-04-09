---
status: validated
resolution: "Config issue; register_as_tool: true required."
experiment: "001"
date: "2026-04-09"
---

# Finding 009: Nested workflow tool not found

Workflows calling other workflows as tools were failing to find the child tool. This was initially flagged as an engine bug.

**Status:** VALIDATED
The issue was a configuration error. Child workflows must have `register_as_tool: true` in their YAML to be added to the engine's tool registry. Once enabled, nested calls function as expected.
