---
status: validated
resolution: "notify_tool_list_changed() was already implemented."
experiment: "003"
date: "2026-04-09"
---

# Finding 003: MCP hot-reload invisible

When a workflow is updated and `config_reload` is called, the MCP client (e.g., Claude Code) does not always discover the changed tool list automatically.

**Status:** VALIDATED (2026-04-09)
The `konf-mcp` server correctly emits a `notifications/tool_list_changed` event. Clients that follow the MCP spec should refresh their tool definitions.
