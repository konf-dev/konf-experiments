---
status: fixed
resolution: "Workflow input stored under 'input' key."
experiment: "001"
date: "2026-04-09"
---

# Finding 006: Template input prefix

When a workflow is called, its input was initially merged directly into the top-level template context, making it hard to distinguish from intermediate node outputs.

**Status:** RESOLVED
The `konf-runtime` now stores workflow input under a dedicated `input` key. Templates use `{{input.my_arg}}` instead of `{{my_arg}}`, which provides clear separation and avoids naming collisions.
