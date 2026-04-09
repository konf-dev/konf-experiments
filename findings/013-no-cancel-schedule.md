---
status: fixed
resolution: "`cancel:schedule` tool was added."
experiment: "004"
date: "2026-04-09"
---
# Finding 013: No cancel_schedule mechanism

The `schedule` tool returns a `schedule_id` but there was no way to cancel a repeating timer. The only way to stop nightwatch was to unregister the workflow (remove YAML + config_reload) — the spawned tokio task checks the registry on each iteration and stops if the workflow is gone.

This was an acceptable kill-switch but a proper `cancel_schedule(schedule_id)` tool was needed for production use.

**Impact:** Low — registry check provides a kill-switch. Track for future.
