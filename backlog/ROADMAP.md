# Konf Roadmap

Driven by experiment findings, not speculation. Each item is something we're building toward — sequenced by what we learn.

---

## Experiment Track

Each experiment proves one more layer. Findings feed back into Konf infra.

### Experiment 001: MCP E2E — connect and validate tools
- [ ] Connect MCP client → konf-mcp → system:introspect → see tools
- [ ] Generate workflow YAML → yaml:validate_workflow → deploy → execute
- [ ] shell:exec in sandbox container
- [ ] config:reload picks up new workflows (now fully implemented)
- [ ] Git checkpointing via shell:exec
- [ ] Capability escalation blocked (permission proof)

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
shell:exec logs, others don't. Need universal tool invocation logging.

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
