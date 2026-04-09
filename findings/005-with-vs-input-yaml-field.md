---
status: fixed
resolution: "Standardized on 'with:' for tool arguments."
experiment: "001"
date: "2026-04-09"
---

# Finding 005: `with:` vs `input:` YAML field

In early experiment stages, some YAML workflows used `input:` while others used `with:` to specify tool arguments.

**Status:** RESOLVED
The parser (`konflux-core/src/parser/schema.rs`) was updated to strictly expect the `with:` key for tool-calling nodes, ensuring consistent configuration across the platform.
