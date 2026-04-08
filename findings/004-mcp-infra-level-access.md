# Finding 004: konf-mcp gives infra-level access with no namespace

**Experiment:** 001
**Date:** 2026-04-08
**Severity:** Medium (acceptable for development, not for production)

## What happened

Every MCP tool call gets a bare `ToolContext` with:
- `capabilities: [tool_name]` — only the specific tool being called
- No namespace
- No actor identity
- No session

The HTTP backend (konf-backend) properly creates scoped contexts with `namespace: konf:default:dev_user` and VirtualizedTool wrapping. konf-mcp does not.

## Why this matters

For the architect (trusted, single-user), this is fine. For production multi-tenant MCP access, this means any MCP client has infra-level access to all tools without namespace isolation.

## Fix needed

MCP session capability grants: auth + session-scoped capability grants for MCP connections. Each MCP session should have an associated namespace and capability set, just like HTTP requests do via JWT claims.
