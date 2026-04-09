---
status: fixed
resolution: "`Engine::notify_tools_changed()` + watch channel → `peer.notify_tool_list_changed()` in konf-mcp background task."
experiment: "001"
date: "2026-04-09"
---
# Finding 003: Hot-registered tools invisible to MCP clients

**Experiment:** 001
**Date:** 2026-04-08
**Severity:** Medium

## What happened

After calling `config:reload`, the engine registered `workflow:hello_world` as a 13th tool (confirmed by `system:introspect`). But `ToolSearch` in Claude Code couldn't find it — the MCP tool list was cached at connection time.

## Root cause

`konf-mcp` wasn't sending `notifications/tools/list_changed` after tools were added or removed. The MCP spec supports this notification, but it was not implemented.

## Fix

The engine now provides a watch channel (`Engine::watch_tools_changed()`). `konf-mcp` spawns a background task that listens on this channel and calls `peer.notify_tool_list_changed()` to inform all connected clients of updates.
