---
status: addressed
resolution: "`scope_from_role()` + `with_capabilities()` implemented. Dev mode keeps full access."
experiment: "001"
date: "2026-04-08"
---
# Finding 004: konf-mcp gives infra-level access with no namespace

**Experiment:** 001
**Date:** 2026-04-08
**Severity:** Medium (acceptable for development, not for production)

## What happened

Every MCP tool call was getting a bare `ToolContext` with only the specific tool's capability, and no namespace, actor identity, or session. The HTTP backend (konf-backend) properly creates scoped contexts, but konf-mcp did not.

## Why this matters

For the architect agent (trusted, single-user), this is fine. For production multi-tenant MCP access, this means any MCP client has infra-level access to all tools without namespace isolation.

## Fix

MCP session capability grants were implemented. Authenticated MCP connections (e.g., via SSE with a JWT) now have their capabilities determined by the user's role, just like the HTTP API. `scope_from_role()` and `with_capabilities()` create a properly scoped context for each MCP call. Unauthenticated local stdio sessions still receive broad access for development purposes.
