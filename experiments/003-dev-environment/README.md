# Experiment 003: Dev Environment as a Konf Product

**Status: ACTIVE**

**Goal:** Can konf manage its own development environment? Create workflows that automate common dev tasks across the konf-dev-stack meta-repo.

**Hypothesis:** Without E2 (agent-mode ai_complete), konf can still automate deterministic dev operations — status checks, test runs, doc validation, build verification — as composable workflows callable via MCP.

## Finding: Sandbox vs Host Access

The current `shell_exec` runs inside the sandbox container, which only has `/workspace/config` mounted. Dev environment workflows need **host-level** access to run `git`, `cargo test`, etc. across the actual repo directories.

**Solution:** Create a separate devkit product config that points konf-mcp at a host-level config directory (no sandbox). Different products = different trust levels. The sandbox is for untrusted agent work. The devkit is for trusted dev automation.

**This is the konf model working correctly** — capability separation via product config, not code changes.

## Checklist

- [x] Create devkit workflows (dev_status, run_tests, run_clippy, build_release)
- [x] Discover sandbox limitation (shell_exec runs in container, not host)
- [ ] Create `products/devkit/config/` with host-level shell tool config
- [ ] Point konf-mcp at devkit config for dev automation
- [ ] Test all workflows via MCP
- [ ] Prove composition: one workflow calls others

## Sessions

- 2026-04-09: Created 4 workflows, discovered sandbox/host access boundary
