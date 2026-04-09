# Experiment 003: Dev Environment as Konf Product

**Status:** ACTIVE
**Date:** 2026-04-09

## Hypothesis

Konf can manage its own development lifecycle — tests, clippy, code review, commits — through agentic workflows using ai_complete with tool-calling.

## Validation Criteria

1. VCS workflows work end-to-end (branch, commit, PR) via konf-agents[bot]
2. ai_complete with tool-calling runs against real LLM
3. Capability enforcement restricts what the LLM can access
4. Streaming events (ToolStart/ToolEnd) are emitted during ReAct loop
5. Nested workflow composition works (dev_status calls run_tests)
