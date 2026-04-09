# Experiment 003: Validation Results

**Date:** 2026-04-09
**Status:** ACTIVE — initial validation complete

## E2 Agent-Mode ai_complete — Verified

### VCS Workflows (via konf-agents[bot])

- [x] `vcs_branch` — creates `agent/devkit/{name}` branches via GitHub MCP
- [x] `vcs_commit` — pushes files, commits attributed to `konf-agents[bot]`
- [x] Commit identity distinct from human (Bert = SouravSharan, bot = konf-agents[bot])
- [x] Full pipeline: Konf workflow → Konf MCP → GitHub MCP → GitHub API

### ai_complete ReAct Loop

- [x] Basic completion works (devstral:24b, qwen3-coder:30b)
- [x] Single tool call: LLM calls echo → gets result → produces final text (2 iterations)
- [x] Multi-tool call: LLM calls echo + concat in parallel → produces final text (2 iterations, 2 tool calls)
- [x] Per-node model override: `qwen3-coder:30b` overrides default `qwen3:8b`
- [x] Tool whitelist: `tools: ["echo", "concat"]` restricts LLM's visible tools
- [x] max_iterations: respected

### Known Issues

- `qwen3:8b` returns empty text — likely thinking/reasoning tokens not captured as AssistantContent::Text
- Streaming events (ToolStart/ToolEnd) not yet verified from MCP client perspective (emitted internally but MCP doesn't expose them yet)

## What's Next

1. Build a real agentic workflow: `dev_status` using ai_complete with shell_exec + memory tools
2. Test capability enforcement: agent without `shell_exec` capability should be denied
3. Test nested workflow composition: workflow calling workflow_run_tests as a tool
4. Fix qwen3:8b empty response issue
