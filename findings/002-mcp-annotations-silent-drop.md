---
status: fixed
resolution: "Annotations serialized as 'annotations' object."
experiment: "001"
date: "2026-04-09"
---

# Finding 002: MCP annotations silent drop

The MCP spec includes a `tool` object with an optional `annotations` field. The `konf-mcp` server was initially flat-serializing metadata, causing clients to ignore critical behavioral hints (like `read_only` or `idempotent`).

**Status:** RESOLVED
The `konf-mcp` implementation now correctly bundles these into the `annotations` key in the JSON response, ensuring clients like Claude Code can optimize their tool-calling behavior.
