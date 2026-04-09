---
status: by-design
resolution: "Direct MCP sessions bypass product-level scoping."
experiment: "001"
date: "2026-04-09"
---

# Finding 004: MCP infra-level access

By default, an MCP connection via `stdio` (like Claude Code) has full `capabilities: ["*"]` access to all tools in the engine. This bypasses the product-level capability lattice defined in `project.yaml`.

**Status:** BY-DESIGN
This is appropriate for local developers (who *are* the infrastructure admins). Scoped access should be enforced at the transport layer (e.g., `konf-backend` or multi-user MCP) via authentication.
