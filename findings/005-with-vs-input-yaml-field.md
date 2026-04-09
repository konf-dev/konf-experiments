---
status: documented
resolution: "Only `with:` exists in the parser. The docs were updated."
experiment: "001"
date: "2026-04-09"
---
# Finding 005: Workflow YAML uses `with:` not `input:` — silent failure

**Experiment:** 001
**Date:** 2026-04-09
**Severity:** High — causes silent data loss (tool receives empty input)

## What happened

A workflow used `input:` to pass parameters to a tool. The workflow executed without error but the tool received empty input, as the `input:` field was silently dropped.

## Root cause

The parser schema (`parser/schema.rs`) defines `with:` as the field for static parameters. There is no `input:` field in `NodeSchema`. Serde's default behavior was to skip unknown fields. The documentation was also inconsistent.

## Fix

The workflow YAML was changed from `input:` to `with:`. The documentation was updated to clarify that `with:` is the only field for node parameters and that it supports both static and `{{template}}` values. `#[serde(deny_unknown_fields)]` was also added to `NodeSchema` to prevent silent failures in the future.
