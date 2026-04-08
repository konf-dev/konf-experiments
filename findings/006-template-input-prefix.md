# Finding 006: Template references require `input.` prefix for workflow arguments

**Experiment:** 001
**Date:** 2026-04-09
**Severity:** Medium — causes clear error, not silent failure

## What happened

Workflow YAML used `{{url}}` to reference the workflow's input parameter. Got error: `"unresolved reference: url"`.

## Root cause

The executor stores workflow input under the key `"input"` in the state map (`executor.rs` line 118: `state.set_output("input", input)`). So to reference a field from the workflow's input, templates must use `{{input.url}}`, not `{{url}}`.

Similarly, to reference output from a previous node named `fetch`, use `{{fetch.body}}`.

## Template reference rules

| Reference | Meaning |
|-----------|---------|
| `{{input.url}}` | Workflow input field `url` |
| `{{fetch.body}}` | Output from node `fetch`, field `body` |
| `{{fetch}}` | Entire output of node `fetch` |

## Documentation impact

The workflow-reference.md docs show `{{message}}` and `{{search.results}}` as examples. The `{{message}}` form only works if there's a node named `message` — for workflow input, it should be `{{input.message}}`.

## Fix applied

Changed `{{url}}` to `{{input.url}}` in fetch_url workflow. Works correctly now.
