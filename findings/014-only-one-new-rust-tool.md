---
status: validated
resolution: "Agent behavior is pure YAML."
experiment: "004"
date: "2026-04-09"
---

# Finding 014: Only one new Rust tool needed

The entire experiment runs on one new Rust tool (`schedule`). Everything else — the agent behavior, logging, guard rules, role definitions — is YAML configuration.

**Status:** VALIDATED
This confirms Principle #9 ("Products are configuration, not code") and Principle #11 ("The kernel does nothing a workflow can do").
