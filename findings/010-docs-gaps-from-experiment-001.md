---
status: fixed
resolution: "`workflow-reference.md` was comprehensively rewritten."
experiment: "001"
date: "2026-04-09"
---
# Finding 010: Documentation gaps found during Experiment 001

**Experiment:** 001
**Date:** 2026-04-09
**Severity:** Medium — causes confusion for anyone using Konf

## Summary of Gaps

During the first end-to-end experiment, numerous documentation gaps were found across YAML schema, MCP integration, configuration, and architecture. These included:

-   **YAML:** `with:` vs `input:`, template prefixes (`input.`), `register_as_tool` requirement, single entry node constraint.
-   **MCP:** `.mcp.json` usage, tool name restrictions, client compatibility issues (annotations), lack of hot-reload notifications.
-   **Configuration:** Env var interpolation support, `KONF_DEV_MODE`, `KONF_CONFIG_DIR`.
-   **Architecture:** Workflow execution model details, child scope tool copying, MCP server limitations.

## Fix

A comprehensive documentation overhaul was planned and executed. The most critical document, `workflow-reference.md`, was completely rewritten to be authoritative and correct. Other documents were updated to address the specific gaps identified.
