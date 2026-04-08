# Konf Roadmap — What Needs Building Next

Prioritized by what the architect experiment will reveal. Items marked with **(blocked)** can't be done until a dependency is resolved.

---

## Now: Validate What's Built

Before building more, prove the current architecture works end-to-end.

- [ ] Connect MCP client → konf-mcp → call system:introspect → see tools
- [ ] Generate workflow YAML via AI → yaml:validate_workflow → deploy → execute
- [ ] shell:exec works in sandbox container
- [ ] config:reload picks up new workflows
- [ ] Git checkpointing via shell:exec works
- [ ] Capability escalation is actually blocked (test the permission proof)

---

## Next: Gaps the Architect Will Expose

These are things we expect to need based on architecture analysis. The experiment will confirm which are real blockers vs theoretical.

### config:reload — Full Implementation
**Priority:** Critical (first thing the architect will need)
**Status:** Stub exists — reports file count but doesn't actually re-register workflows
**What's needed:** Re-parse workflows dir, re-register as tools, ArcSwap the product config. The boot sequence in konf-init already does this — extract it into a reusable function.

### Conversation State / Multi-Turn
**Priority:** High
**Status:** Not implemented
**What's needed:** The MCP connection is stateless per-call. For the architect to have a conversation (remember what it built earlier in the session), either:
- The MCP client handles conversation state (Claude Code does this natively)
- Or Konf needs session state for MCP connections
**Likely finding:** Claude Code handles this. Other MCP clients might not.

### File Watcher (notify crate)
**Priority:** Medium
**Status:** notify is a dependency but not wired
**What's needed:** Watch config dir for changes, auto-reload. Alternative to manual config:reload calls. Less urgent if config:reload works well.

### Audit Journal for MCP Calls
**Priority:** Medium
**Status:** shell:exec logs to journal, but other MCP tool calls don't
**What's needed:** All tool invocations via MCP should be logged to the event journal, not just shell:exec.

---

## Later: Architecture Expansion

### SQLite Memory Backend
**Why:** Proves backend-agnostic storage. Enables edge/air-gapped/mobile.
**Blocked on:** Nothing — can build anytime
**Validates:** MemoryBackend trait is a real abstraction, not leaky

### WASM Tool Adapter
**Why:** Proves open adapter architecture. Enables admin-installed plugins without recompilation.
**Blocked on:** Nothing — can build anytime
**Validates:** Tool trait is genuinely adapter-agnostic

### Autonomous Agent Mode
**Why:** Agent runs without human steering (for scheduled tasks, background processing)
**Blocked on:** The ai:complete tool already has a ReAct loop via rig-core. Need to verify it works with the new tools (shell:exec, validate, introspect) as inner tools.
**Validates:** Same tools work in both MCP (human-steered) and autonomous (agent-steered) modes

### Network Access for Sandbox
**Why:** Architect needs to test HTTP workflows it creates
**Blocked on:** Sandbox currently runs with `--network none`
**What's needed:** Docker network with egress filtering (allow specific domains only)

### Capability Grants per MCP Session
**Why:** Different MCP clients should get different capabilities (admin vs user)
**Blocked on:** konf-mcp currently gives all tools to all clients
**What's needed:** Auth + session-scoped capability grants for MCP connections

### Product Packaging
**Why:** Once the architect builds a product, how do you ship it? A product is a config directory — needs a standard packaging format.
**What's needed:** Define what a "distributable product" looks like (tarball? git repo? OCI image with config?)

---

## Far Future: Vision Items

These are architecturally sound but premature without users.

| Item | When to build |
|------|--------------|
| UniFFI mobile bindings (iOS/Android) | When a product needs to run on mobile |
| P2P agent swarms (libp2p/WebRTC) | When multi-instance is needed |
| Suspend/resume checkpointing | When a product has long-running workflows |
| Dynamic capability elevation ("ask user for permission") | When a product needs it |
| Firecracker/gVisor upgrade | When untrusted agents need sandboxing |
| Formal API spec (OpenAPI) | When external developers need stable API contracts |
| Admin console (Appsmith) | When operators need a UI |

---

## How This List Grows

Every experiment in `experiments/` will produce findings in `findings/`. Findings that reveal Konf gaps get added here. The roadmap is driven by real friction, not speculation.
