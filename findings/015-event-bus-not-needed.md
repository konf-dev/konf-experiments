---
status: by-design
resolution: "Push-based (timer → workflow) is sufficient for proactive."
experiment: "004"
date: "2026-04-09"
---

# Finding 015: Event bus (F2) not needed for autonomous agents

The push-based model (repeating timer → workflow → tools) is sufficient for autonomous operation. No event-driven triggers were needed. The nightwatch agent runs, checks health, logs results — all without subscribing to events. 

**Status:** BY-DESIGN
F2 (event bus) would be needed for reactive agents ("when test fails, trigger diagnosis"), but for proactive agents ("check health every N seconds"), the schedule primitive is enough. Defer F2 until an experiment demonstrates a reactive use case.
