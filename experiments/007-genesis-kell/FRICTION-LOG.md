# Experiment 007 — Friction Log

> Every surprise, every deferral, every kernel gap encountered while building genesis.kell.

## Format

Each entry is a dated H2 section. Categories: `kernel-gap`, `config-gap`, `prompt-gap`, `infra-gap`, `doctrine-drift`, `surprise`.

```markdown
## YYYY-MM-DD — short title
**Category:** kernel-gap | config-gap | prompt-gap | infra-gap | doctrine-drift | surprise
**Phase:** 0 | 0.5 | 1 | ... | 5
**Severity:** low | medium | high

### What happened
[1 paragraph]

### What we tried
[bullets or short paragraph]

### Outcome
[Fixed in place | Deferred to backlog entry `<id>` | Escalated to konf kernel issue `<link>` | Still open]

### Links
[commits, PRs, backlog entries, kernel issues]
```

---

## 2026-04-10 — experiment reset
**Category:** doctrine-drift
**Phase:** 0
**Severity:** n/a (retrospective)

### What happened
The prior experiment 007 scaffold drifted from first principles: speculative `spawn_kell`/`system_pulse` workflows committed as code before any friction justified them, a "Second-in-Command" framing that oversold the kell's role, an Infisical integration layered in before a single secret needed rotating, and a dangling reference to `ghcr.io/konf-dev/kernel:latest` (an image that does not exist).

### What we tried
Rather than patching the drift, we reset `konf-genesis.kell` to empty and rebuilt Phase 0 from first principles. The reframing: genesis is a **bootstrap proof** — kell #1, the minimum viable bundle that runs on konf and hosts a Broker agent who helps build everything else.

### Outcome
Rebuilt on branch `reset/v0`. New Phase 0 deliverables: `README.md`, `docs/{spec,doctrine,secrets,control-plane}.md`, `operator/{project,tools}.yaml`, `env.example/*`, `.gitignore`, `backlog/README.md`. Broker persona deferred to Phase 0.5 as a standalone publishable artifact.

### Links
- Plan: `~/.claude/plans/nifty-sniffing-cake.md`
- Repo: `konf-genesis.kell` branch `reset/v0`
