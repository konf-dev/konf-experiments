# Brief: Add env var interpolation to product config (tools.yaml, models.yaml)

## Problem

Product config files (`tools.yaml`, `models.yaml`) don't support `${VAR:-default}` env var interpolation. The YAML string is passed directly to `serde_yaml::from_str()`. Users get a confusing runtime error when they try to use env vars (e.g., model name becomes the literal string `"${KONF_MODEL:-qwen3:8b}"`).

**Finding reference:** `konf-experiments/findings/007-tools-yaml-no-env-interpolation.md`

## Location

Single file to modify:

- `crates/konf-init/src/lib.rs` — line 83, where `serde_yaml::from_str::<ProductConfig>(&contents)` is called

The fix is to pre-process the YAML string before parsing, replacing `${VAR}` and `${VAR:-default}` patterns with their environment variable values.

## Root Cause

Platform config (`konf.toml`) gets env var support for free via figment's `Env` provider. Product config uses raw `serde_yaml::from_str()` with no interpolation step.

## Approach

Add a `interpolate_env_vars(input: &str) -> String` function that:
1. Finds all `${VAR}` and `${VAR:-default}` patterns using a simple regex or manual parser
2. Replaces `${VAR}` with the value of env var `VAR`, or empty string if not set
3. Replaces `${VAR:-default}` with the value of env var `VAR`, or `"default"` if not set
4. Returns the processed string

Call it before `serde_yaml::from_str()`:
```rust
let interpolated = interpolate_env_vars(&contents);
serde_yaml::from_str::<ProductConfig>(&interpolated)?
```

Use the `regex` crate if it's already a dependency, otherwise implement with simple string scanning (no new deps).

## Success Criteria

- [ ] `cd konf && cargo test --workspace` — all tests pass
- [ ] `cd konf && cargo clippy --workspace -- -D warnings` — no warnings
- [ ] `${EXISTING_VAR}` is replaced with the env var value
- [ ] `${MISSING_VAR}` is replaced with empty string
- [ ] `${MISSING_VAR:-fallback}` is replaced with `"fallback"`
- [ ] `${EXISTING_VAR:-fallback}` is replaced with the env var value (not fallback)
- [ ] Add unit tests for the `interpolate_env_vars` function covering all 4 cases above
- [ ] No changes to files outside of `crates/konf-init/`

## Constraints

- Only modify `crates/konf-init/src/lib.rs`
- No `.unwrap()` or `.expect()` in the interpolation function
- Prefer no new dependencies — use simple string scanning if `regex` is not already a dep
- The function must be infallible (return String, not Result) — missing vars are replaced with empty/default, never error
- Add `#[cfg(test)]` unit tests in the same file

## Context

Read these files first:

- `CLAUDE.md` — project philosophy (at repo root `/home/bert/Work/orgs/konf-dev-stack/CLAUDE.md`)
- `crates/konf-init/src/lib.rs` — lines 78-87, the product config loading section
- `crates/konf-init/Cargo.toml` — check if `regex` is already a dependency
