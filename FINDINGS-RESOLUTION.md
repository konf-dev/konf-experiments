| # | Title | Status | Resolution |
|---|---|---|---|
| 001 | Tool names with colons break MCP | RESOLVED | Colons restored. MCP adapter translates at boundary per SEP-986. |
| 002 | MCP annotations cause silent tool drop | FIXED | Annotations not serialized to avoid Claude Code bug. |
| 003 | Hot-registered tools invisible to MCP | ALREADY WORKED | `notify_tool_list_changed()` was already implemented. |
| 004 | konf-mcp gives infra-level access | ADDRESSED | `scope_from_role()` + `with_capabilities()`. Dev mode keeps full access. |
| 005 | YAML uses `with:` not `input:` | DOCUMENTED | Only `with:` exists in parser. Docs updated. |
| 006 | Template refs need `{{input.x}}` prefix | DOCUMENTED | Workflow input stored under `input` key. Docs updated. |
| 007 | tools.yaml env interpolation | ALREADY WORKED | `interpolate_env_vars()` was implemented. Docs were wrong. |
| 008 | Single entry node design | BY DESIGN | First YAML node = entry. Parallel via `then: [a, b]`. |
| 009 | Nested workflow tools not found | VERIFIED | Was config issue (missing `register_as_tool: true`), not code bug. |
| 010 | Comprehensive docs gaps | FIXED | `workflow-reference.md` comprehensive rewrite. |
| 011 | `catch:` schema mismatch | FIXED | `CatchBlock` is now an untagged enum accepting a string or array of branches. |
| 012 | MCP sessions bypass tool guards | BY DESIGN | Guards at runtime scope, not MCP layer. Matches Linux model (root user bypasses). |
| 013 | No cancel_schedule mechanism | FIXED | `cancel:schedule` tool added. |
| 014 | Only one new Rust tool needed for autonomy | VALIDATED | `schedule` tool is the only kernel addition for autonomous agents. |
| 015 | Event bus (F2) not needed for autonomy | VALIDATED | Push-based scheduling sufficient for proactive agents. |
