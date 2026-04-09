---
status: resolved
resolution: "Colons restored. MCP adapter translates at boundary per SEP-986."
experiment: "001"
date: "2026-04-08"
---
# Finding 001: Tool names with colons break MCP client integration

**Experiment:** 001 — The Architect
**Date:** 2026-04-08
**Severity:** Blocker — tools don't register at all
**Issue:** konf-dev/konf#4

## What happened

Connected Claude Code to konf-mcp via `.mcp.json`. The MCP server connects successfully — its instruction text ("Konf AI agent platform...") appears in the system prompt. But zero tools show up in Claude Code's deferred tool list.

## Root cause

Tool names use colon-separated namespacing: `shell:exec`, `memory:search`, `ai:complete`. Early MCP clients mapped MCP tool names to internal identifiers as `mcp__server__toolname`. Colons in tool names produced invalid identifiers, so tools silently failed to register.

While client support has improved, the formal MCP spec (SEP-986) still restricts tool names to `[A-Za-z0-9_\-.]`, making colons non-compliant.

## Fix

The kernel's canonical tool names use colons. An adapter in `konf-mcp` translates them to underscores (`shell_exec`) for MCP clients, ensuring spec compliance at the boundary.

## Lesson

Test MCP integration with actual clients early. The MCP spec allows any string as a tool name, but clients and the formal spec have their own naming constraints. Don't assume spec compliance = client compatibility. The adapter pattern provides a clean separation of concerns.
