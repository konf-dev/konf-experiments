---
status: fixed
resolution: "`interpolate_env_vars()` pre-processes YAML before parsing."
experiment: "001"
date: "2026-04-09"
---
# Finding 007: tools.yaml does not support ${VAR:-default} env interpolation

**Experiment:** 001
**Date:** 2026-04-09
**Severity:** Medium — causes runtime error with clear message

## What happened

`tools.yaml` used `model: "${KONF_MODEL:-qwen3:8b}"`. The LLM provider received the literal string as the model name, causing a `400 Bad Request`.

## Root cause

Platform config (`konf.toml`) supports env var interpolation via figment's `Env` provider. Product config (`tools.yaml`) was being loaded via raw `serde_yaml::from_str()` with no interpolation step. The documentation incorrectly claimed it was supported.

## Fix

A pre-processing step, `interpolate_env_vars()`, was added to the product config loader. It handles `${VAR}` and `${VAR:-default}` syntax before the YAML is parsed by Serde.
