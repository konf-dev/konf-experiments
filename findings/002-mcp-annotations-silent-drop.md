---
status: fixed
resolution: "Annotations are not serialized to MCP to avoid a client bug."
experiment: "001"
date: "2026-04-08"
---
# Finding 002: MCP annotations cause Claude Code to silently drop ALL tools

**Experiment:** 001
**Date:** 2026-04-08
**Severity:** Blocker
**Issue:** anthropics/claude-code#25081

## What happened

konf-mcp server connects and shows as "connected" in Claude Code's /mcp dialog. Server instruction text appears. But zero tools show up — ToolSearch returns nothing.

## Root cause

Two issues:

1.  **Annotations field (PRIMARY):** The `tool_info_to_mcp()` function called `.with_annotations()` on every tool, serializing the MCP 2025-11-25 annotations field. An early version of the Claude Code client could not parse this field and silently dropped the entire tool list. This bug was closed as "not planned".

2.  **Empty inputSchema (SECONDARY):** The `echo` and `log` builtin tools had `inputSchema: {}` without `"type": "object"`. MCP spec requires `type: "object"` at minimum.

## Fix

-   Removed `.with_annotations()` from `tool_info_to_mcp()` in konf-mcp.
-   Changed `json!({})` to `json!({"type": "object"})` for echo and log tools.

## Lesson

MCP spec compliance ≠ client compatibility. Always test with actual clients. Some MCP clients may not support newer spec features and can fail silently rather than gracefully degrading.
