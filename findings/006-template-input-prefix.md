---
status: documented
resolution: "Workflow input is stored under the `input` key. Docs updated."
experiment: "001"
date: "2026-04-09"
---
# Finding 006: Template references require `input.` prefix

**Experiment:** 001
**Date:** 2026-04-09
**Severity:** Medium — causes clear error, not silent failure

## What happened

A workflow used `{{url}}` to reference one of its own input parameters, which failed with `"unresolved reference: url"`.

## Root cause

The executor stores the entire workflow input object under the key `"input"` in the state map. To reference a field from the workflow's input, templates must use `{{input.url}}`.

## Template reference rules

| Reference | Meaning |
|-----------|---------|
| `{{input.url}}` | Workflow input field `url` |
| `{{fetch.body}}` | Output from node `fetch`, field `body` |
| `{{fetch}}` | Entire output of node `fetch` |

## Fix

The workflow was changed to use `{{input.url}}`. The documentation (`workflow-reference.md`) was updated to reflect this requirement.
