---
status: fixed
resolution: "Colons restored. MCP adapter translates at boundary."
experiment: "001"
date: "2026-04-09"
---

# Finding 001: Tool names with colons break MCP

Tool names use colon-separated namespacing: `shell:exec`, `memory:search`, `ai:complete`. Claude Code maps MCP tool names to internal identifiers as `mcp__server__toolname`. Colons in tool names produce invalid identifiers, so tools silently fail to register.

**Status:** RESOLVED
The kernel uses colons internally. The `konf-mcp` server translates colons to underscores (`_`) in the tool list sent to the MCP client.

```yaml
# Internal (workflow)
do: shell:exec

# MCP (wire)
name: shell_exec
```

This ensures compatibility with the MCP spec and clients while maintaining the kernel's preferred namespacing.
