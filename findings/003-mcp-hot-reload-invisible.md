# Finding 003: Hot-registered tools invisible to MCP clients

**Experiment:** 001
**Date:** 2026-04-08
**Severity:** Medium
**Status:** FIXED (2026-04-09) — `Engine::notify_tools_changed()` + watch channel → `peer.notify_tool_list_changed()` in konf-mcp background task

## What happened

After calling `config_reload`, the engine registered `workflow_hello_world` as a 13th tool (confirmed by `system_introspect`). But `ToolSearch` in Claude Code couldn't find it — the MCP tool list was cached at connection time.

## Root cause

konf-mcp doesn't send `notifications/tools/list_changed` after tools are added or removed. The MCP spec supports this notification, but it's not implemented.

## Workaround

Restart the MCP client to see new tools.

## Fix needed

After `config_reload` modifies the tool registry, konf-mcp should send `notifications/tools/list_changed` to all connected clients. This requires konf-mcp to hold a reference to the MCP service/session to send notifications.
