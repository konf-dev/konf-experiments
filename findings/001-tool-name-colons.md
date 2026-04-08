# Finding 001: Tool names with colons break MCP client integration

**Experiment:** 001 — The Architect
**Date:** 2026-04-08
**Severity:** Blocker — tools don't register at all
**Issue:** konf-dev/konf#4

## What happened

Connected Claude Code to konf-mcp via `.mcp.json`. The MCP server connects successfully — its instruction text ("Konf AI agent platform...") appears in the system prompt. But zero tools show up in Claude Code's deferred tool list.

## Root cause

Tool names use colon-separated namespacing: `shell:exec`, `memory:search`, `ai:complete`. Claude Code maps MCP tool names to internal identifiers as `mcp__server__toolname`. Colons in tool names produce invalid identifiers, so tools silently fail to register.

## Evidence

```bash
# MCP tools/list returns 12 tools, all with colons
printf '...' | konf-mcp --config ... | python3 -c "..."
# Output:
#   http:get
#   shell:exec
#   system:introspect
#   yaml:validate_workflow
#   ...
```

ToolSearch for any konf tool returns nothing.

## Fix

Rename `prefix:name` → `prefix_name`. Filed as konf-dev/konf#4.

## Lesson

Test MCP integration with actual clients early. The MCP spec allows any string as a tool name, but clients have their own naming constraints. Don't assume spec compliance = client compatibility.
