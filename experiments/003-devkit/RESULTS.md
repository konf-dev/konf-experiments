# Experiment 003: Validation Results

**Date:** 2026-04-09
**Status:** COMPLETE

## All Validation Criteria Met

### 1. VCS Workflows via konf-agents[bot]

- [x] `vcs_branch` — creates `agent/devkit/{name}` branches via GitHub MCP
- [x] `vcs_commit` — pushes files, commits attributed to `konf-agents[bot]`
- [x] Commit identity distinct from human (Bert = SouravSharan, bot = konf-agents[bot])
- [x] Full pipeline: Konf workflow → Konf MCP → GitHub MCP → GitHub API

### 2. ai_complete with Tool-Calling (ReAct Loop)

- [x] Basic completion (devstral:24b, qwen3-coder:30b)
- [x] Single tool call: echo → 2 iterations, 1 tool call
- [x] Multi-tool parallel: echo + concat → 2 iterations, 2 tool calls
- [x] Per-node model override: qwen3-coder:30b overrides default qwen3:8b
- [x] Tool whitelist: `tools: ["echo", "concat"]` restricts LLM visibility
- [x] max_iterations enforcement: respected

### 3. AI-Generated Workflow → Validate → Commit

- [x] ai_complete generated `summarize_topic.yaml` (8 iterations, 7 tool calls)
- [x] yaml_validate_workflow confirmed valid (3 nodes)
- [x] vcs_commit pushed to agent branch as konf-agents[bot]
- [x] This is the E3 (products/architect) pattern proven end-to-end

### 4. Nested Workflow Composition

- [x] `nested_test` workflow calls `workflow_hello_world` as a child tool
- [x] Parent receives child output and uses it in subsequent nodes
- [x] Output: "Child said: Hello from a workflow generated via MCP!"
- [x] Hot-reload via config_reload picks up new workflow (56 tools registered)

### 5. Capability Enforcement

- [x] Tool whitelist AND-filters with capabilities
- [x] ai_complete self-excluded from inner tools (prevents recursion)
- [x] Empty capabilities deny all tools

## Known Issues

- `qwen3:8b` returns empty text — thinking/reasoning tokens not captured as AssistantContent::Text
- Streaming events (ToolStart/ToolEnd) emitted internally but not exposed via MCP protocol

## Significance

Experiment 003 validates the full E2 implementation:
- The kernel owns the ReAct loop (not rig, not application code)
- Capability enforcement is structural (not prompt-based)
- VCS operations are composable workflows over MCP tools (VCS-agnostic)
- Agent identity is distinct and accountable (GitHub App)
- Nested composition works (workflows calling workflows)
- Self-modification is safe (generate YAML → validate → deploy)

This proves Konf can manage its own development lifecycle through agentic workflows.
