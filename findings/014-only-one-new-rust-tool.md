---
status: validated
resolution: "`schedule` tool is the only kernel addition for autonomous agents."
experiment: "004"
date: "2026-04-09"
---
# Finding 014: The `schedule` tool is the only new Rust needed

The entire autonomous agent experiment runs on one new Rust tool (`schedule`, ~80 lines). Everything else — the agent behavior, logging, guard rules, role definitions — is YAML configuration. This validates Principle 11 ("the kernel does nothing a workflow can do") and Principle 9 ("products are configuration, not code").
