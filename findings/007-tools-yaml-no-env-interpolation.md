# Finding 007: tools.yaml does not support ${VAR:-default} env interpolation

**Experiment:** 001
**Date:** 2026-04-09
**Severity:** Medium — causes runtime error with clear message
**Status:** FIXED (2026-04-09) — `interpolate_env_vars()` pre-processes YAML before parsing, supports `${VAR}` and `${VAR:-default}`

## What happened

`tools.yaml` had `model: "${KONF_MODEL:-qwen3:8b}"`. The LLM provider received the literal string `"${KONF_MODEL:-qwen3:8b}"` as the model name, causing `400 Bad Request: model is required`.

## Root cause

Platform config (`konf.toml`) supports env var interpolation via figment's `Env` provider. Product config (`tools.yaml`) is loaded via raw `serde_yaml::from_str()` with no interpolation step.

The docs (`docs/admin-guide/security.md`) claim `tools.yaml` supports `${VAR:-default}` interpolation. This is incorrect.

## Fix applied

Hardcoded the model name: `model: "qwen3:8b"`. Works correctly.

## Recommendation

Either:
1. Implement env var interpolation in tools.yaml loading (pre-process the YAML string before parsing)
2. Or update docs to clarify that only konf.toml supports env vars, and tools.yaml uses literal values
