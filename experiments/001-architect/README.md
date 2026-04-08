# Experiment 001: The Architect

**Status: COMPLETE** (2026-04-08)

**Goal:** Connect an MCP client to Konf and build a product through conversation.

**Hypothesis:** The four new tools (shell_exec, yaml_validate_workflow, system_introspect, config_reload) are sufficient for an AI to create, validate, and deploy Konf workflows without writing Rust code.

**Result:** Hypothesis confirmed. All 6 checklist items passed.

## Checklist

- [x] Connect MCP client → konf-mcp → system_introspect → see tools (12 tools)
- [x] Generate workflow YAML → yaml_validate_workflow → deploy → execute
- [x] shell_exec in sandbox container
- [x] config_reload picks up new workflows (13th tool registered)
- [x] Git checkpointing via shell_exec
- [x] Capability escalation blocked (permission proof)

## What Happened

1. Built konf-mcp release binary, started sandbox container
2. Connected Claude Code via `.mcp.json` — server showed "connected" but zero tools appeared
3. **Finding 001:** Tool names with colons (`shell:exec`) break Claude Code's MCP integration. Renamed to underscores (`shell_exec`). Still no tools.
4. **Finding 002:** MCP annotations field causes Claude Code to silently drop ALL tools (anthropics/claude-code#25081). Removed `.with_annotations()`. Tools appeared.
5. Called `system_introspect` — 12 tools visible
6. Generated `hello_world` workflow YAML, validated via `yaml_validate_workflow` — kernel said valid
7. Wrote to sandbox via `shell_exec`, git committed
8. Called `config_reload` — workflow registered as 13th tool (`workflow_hello_world`)
9. **Finding 003:** Hot-registered tools don't appear in MCP clients without reconnection (needs `notifications/tools/list_changed`)
10. Started konf-backend in dev mode, executed workflow via HTTP — returned `"Hello from a workflow generated via MCP!"`
11. **Finding 004:** konf-mcp gives infra-level access (no namespace, no capability restriction)
12. Tested capability escalation: workflow requiring `nonexistent_tool` correctly rejected

## Findings

| # | Finding | Severity | Fix |
|---|---------|----------|-----|
| 001 | Tool names with colons break Claude Code MCP | Blocker | Renamed to underscores (konf-dev/konf#4) |
| 002 | MCP annotations cause silent tool drop | Blocker | Removed annotations from response |
| 003 | Hot-registered tools invisible to MCP clients | Medium | Needs notifications/tools/list_changed |
| 004 | konf-mcp gives infra-level access | Medium | Needs session capability grants |

## Sessions

- [session-2026-04-08.md](session-2026-04-08.md)
