# Findings Resolution Matrix

This document tracks the status and resolution of all architectural findings discovered during Konf experiments.

| #   | Title                                  | Status    | Resolution                                                    |
|-----|----------------------------------------|-----------|---------------------------------------------------------------|
| 001 | Tool names with colons break MCP       | RESOLVED  | Colons restored. MCP adapter translates at boundary per SEP-986. |
| 002 | MCP annotations silent drop            | RESOLVED  | Annotations serialized as `annotations` object, not flat keys. |
| 003 | MCP hot-reload invisible               | VALIDATED | `notify_tool_list_changed()` was already implemented.          |
| 004 | MCP infra-level access                 | BY-DESIGN | MCP sessions use `capabilities: ["*"]` for operator access.    |
| 005 | `with:` vs `input:` YAML field         | RESOLVED  | Standardized on `with:` for tool arguments in `parser/schema.rs`.|
| 006 | Template input prefix                  | RESOLVED  | Workflow input stored under `input` key; use `{{input.x}}`.     |
| 007 | `tools.yaml` env interpolation         | VALIDATED | `interpolate_env_vars()` is implemented; docs were updated.    |
| 008 | Single entry node / fan-out            | BY-DESIGN | Use `then: [a, b]` for parallel execution from the entry node. |
| 009 | Nested workflow tool not found         | VALIDATED | Config issue (missing `register_as_tool: true`), not a bug.    |
| 010 | Docs gaps from experiment 001          | RESOLVED  | Documentation & Code Coherence Overhaul (2026-04-09).          |
| 011 | `catch:` schema mismatch               | RESOLVED  | Both forms (`Simple(String)` and `Branches`) already implemented in `schema.rs:136-149`; test added for the bare-string form (2026-04-11). |
| 012 | MCP sessions bypass tool guards        | BY-DESIGN | Guards apply to scoped engines (workflows), not direct MCP.    |
| 013 | No cancellation for `schedule`         | RESOLVED  | `cancel:schedule` tool implemented (2026-04-09).              |
| 014 | Only one new Rust tool needed          | VALIDATED | Validates "Product is config" (Principle #9).                  |
| 015 | Event bus not needed for autonomous    | BY-DESIGN | Push-based (timer → workflow) is sufficient for proactive.     |
| 016 | Cell vs Kell Architecture              | DROPPED   | Concept removed 2026-04-11 during docs overhaul. See `konf/docs/MENTAL_MODEL.md`. Only unit is "product". |

---

*Last updated: 2026-04-11 (finding 011 resolved).*
