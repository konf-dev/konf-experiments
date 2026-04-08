# Finding 005: Workflow YAML uses `with:` not `input:` — silent failure

**Experiment:** 001
**Date:** 2026-04-09
**Severity:** High — causes silent data loss (tool receives empty input)

## What happened

Workflow YAML used `input:` to pass parameters to a tool:
```yaml
nodes:
  greet:
    do: echo
    input:
      message: "Hello"
```

The workflow executed without error but the echo tool received `{}` — empty input. The `message` field was silently dropped.

## Root cause

The parser schema (`parser/schema.rs`) defines `with:` as the field for static parameters. There is no `input:` field in `NodeSchema`. Serde's default behavior skips unknown fields, so `input:` is silently ignored.

The correct YAML:
```yaml
nodes:
  greet:
    do: echo
    with:
      message: "Hello"
```

## How we found it

1. Ran workflow via MCP — got `{}` instead of `{"message": "..."}`
2. Ran with `RUST_LOG=debug` — traced through executor, saw `resolve_inputs` working on `step.input` which was empty
3. Checked `Step` struct — only has `input: HashMap<String, Expr>` (populated from `with:`)
4. Checked `parser/schema.rs` — confirmed only `with:` exists, no `input:` field
5. Checked `parser/compiler.rs` line 57 — `node.with` maps to `step.with_input()`

## Documentation inconsistency

`docs/product-guide/workflow-reference.md` documents both `with:` (static) and `input:` (dynamic/templated) as valid fields. But the parser only recognizes `with:`. The `input:` field referenced in docs doesn't exist in the schema.

This needs investigation: is `input:` supposed to exist for templated values, or should `with:` handle both static and templated values? Currently `with:` values go through `value_to_expr()` which handles `{{template}}` syntax, so `with:` already supports both.

## Fix applied

Changed workflow YAML from `input:` to `with:`. Output now returns correctly.

## Recommendation

1. Add `#[serde(deny_unknown_fields)]` to `NodeSchema` so invalid fields cause parse errors instead of silent drops
2. Clarify docs: `with:` is the only field for node parameters (supports both static and `{{template}}` values)
3. Or: add `input:` as an alias for `with:` if the distinction is intentional
