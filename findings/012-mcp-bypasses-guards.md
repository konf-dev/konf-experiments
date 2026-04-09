---
status: by-design
resolution: "Guards are at runtime scope, not MCP layer. Matches Linux model (root user bypasses)."
experiment: "004"
date: "2026-04-09"
---
# Finding 012: MCP sessions bypass tool guards

Tool guards are applied in `build_scoped_engine()` during workflow execution, not at the MCP layer. Direct MCP tool calls (`mcp__konf__shell_exec`) bypass guards entirely because the MCP session has `capabilities: ["*"]` and no scoped engine is built for direct calls.

This is correct for local development (the human operator has full access), but means guards only protect _workflows_, not _direct tool calls_. For multi-tenant MCP, guards would need to also be applied in `mcp_tool_context()`.

**Impact:** Low for current use (single-user dev). Important for multi-tenant.
