---
status: validated
resolution: "interpolate_env_vars() was already implemented."
experiment: "001"
date: "2026-04-09"
---

# Finding 007: `tools.yaml` env interpolation

The `tools.yaml` file was initially suspected to skip environment variable interpolation (e.g., `${API_KEY}`).

**Status:** VALIDATED (2026-04-09)
The `konf-init` crate includes an `interpolate_env_vars()` function that correctly processes both `${VAR}` and `${VAR:-default}` syntax before parsing YAML. The documentation was updated to confirm this.
