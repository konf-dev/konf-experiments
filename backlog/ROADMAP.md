# Konf Roadmap

Driven by experiment findings, not speculation. Each item is something we're building toward — sequenced by what we learn.

---

## Experiment Track

Each experiment proves one more layer. Findings feed back into Konf infra.

### Experiment 001: MCP E2E — COMPLETE (2026-04-08)
- [x] Connect MCP client → konf-mcp → system_introspect → see tools (12 tools)
- [x] Generate workflow YAML → yaml_validate_workflow → deploy → execute
- [x] shell_exec in sandbox container
- [x] config_reload picks up new workflows (workflow_hello_world registered)
- [x] Git checkpointing via shell_exec
- [x] Capability escalation blocked (permission proof)
- Findings: [001](../findings/001-tool-name-colons.md), [002](../findings/002-mcp-annotations-silent-drop.md), [003](../findings/003-mcp-hot-reload-invisible.md), [004](../findings/004-mcp-infra-level-access.md)

### Experiment 002: Cross-AI collaboration
- [ ] Claude Code + Gemini CLI in same workspace
- [ ] Shared context via files (experiment logs, docs, git)
- [ ] Each AI audits the other's work
- [ ] Findings: what communication patterns work?

### Experiment 003: Dev environment as a Konf product
- [ ] konf-dev-stack workflow (repos, branches, sync, docs) as Konf config
- [ ] Managing konf + smrti + unspool + autokore from one interface
- [ ] The Oracle — single point of contact with the entire OS

### Experiment 004: Autonomous agent mode
- [ ] ai:complete with inner tools runs without human
- [ ] Scheduled workflows
- [ ] Agent generates and deploys workflows on its own

### Experiment 005: Personal assistant rebuild
- [ ] Unspool's functionality as pure Konf config
- [ ] Memory, personality, scheduling, ADHD-specific features

---

## Infra Gaps

### config:reload — DONE
Fully implemented. Re-parses workflows dir, removes stale tools, re-registers. Engine::remove_tool() added.

### Conversation State
MCP clients handle their own (Claude Code does natively). May need Konf-side session tracking for other clients.

### File Watcher (notify)
Dependency exists, not wired. Auto-reload alternative to manual config:reload.

### Audit Journal for All MCP Calls
shell_exec logs, others don't. Need universal tool invocation logging.

### MCP notifications/tools/list_changed (from Finding 003)
Hot-registered tools (via config_reload) don't appear in MCP clients without reconnection. konf-mcp needs to send `notifications/tools/list_changed` when the tool registry changes.

### MCP Session Capability Grants (from Finding 004)
konf-mcp currently gives infra-level access — bare ToolContext with no namespace or capability restriction. Needs auth + session-scoped grants for production multi-tenant MCP access.

---

## Architecture Expansion

| Item | Why | Build when |
|------|-----|-----------|
| SQLite backend | Proves backend-agnostic storage | After experiment 001 |
| WASM adapter | Proves open adapter architecture | After experiments show which tools people need |
| Multi-repo orchestration | Managing konf+smrti+unspool as unified system | Experiment 003 |
| Cross-AI coordination | Claude+Gemini working together | Experiment 002 |
| The Oracle | Single interface for entire ecosystem | After experiments 001-003 |
| Network for sandbox | Test HTTP workflows | When architect needs it |
| Per-session MCP capabilities | Auth + scoped grants | When multi-user |
| Product packaging | Ship a product (tarball/git/OCI) | When a product is ready to ship |
| Autonomous mode | Agent without human | Experiment 004 |

---

## Vision Items (build when needed)

| Item | When |
|------|------|
| UniFFI mobile bindings | Product needs mobile |
| P2P agent swarms | Multi-instance needed |
| Suspend/resume | Long-running workflows |
| Dynamic capability elevation | "Ask user for permission" flow |
| Firecracker/gVisor | Untrusted agents |
| OpenAPI spec | External devs need stable contracts |
| Admin console | Operators need a UI |

---

## Research Track

Document the process for blog posts, papers, startup pitch:
- How do AI-steered dev workflows compare to traditional?
- What patterns emerge when the product IS the development tool?
- How do multiple AI systems collaborate on a shared codebase?
- What's the minimum viable Agent OS?

---

## Resources

- Claude Code Max, Gemini CLI Pro, ~$8 OpenCode Zen
- Time and focus are the real constraints
- Each experiment: 1-2 sessions max before checkpoint
