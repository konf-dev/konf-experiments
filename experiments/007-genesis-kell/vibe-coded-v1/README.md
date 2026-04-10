# Vibe-coded v1 — rescued evidence

These files were rescued from the `konf-genesis.kell` repo (since renamed to `konf-genesis`) before the `main` branch was archived and the project reset.

## Why

The first attempt at genesis accumulated enough drift that a reset was cheaper than a retrofit. F-002 documents ten drifts observed during the VM bootstrap session — most were "X is wrong/incomplete" rather than execution errors, which is the signature of vibe-coded YAML rather than grounded design.

The decision: delete the old repo content, start fresh from the simplest possible frame — Claude Code running on a VM with `konf-dev-stack` cloned. These two files are the only things worth carrying forward because they are *evidence*, not decoration.

## Contents

- **`friction-log.md`** — in-session friction log from the `konf-genesis.kell` repo. Authoritative record of F-002 (the ten drifts from the VM bootstrap session) and earlier entries. The outer experiment-level log at `../FRICTION-LOG.md` is a different, higher-level artefact; this one is the detailed in-session evidence.
- **`threads.md`** — working-thread log. T-0040 (Tailscale ACL isolation) is the only thread filed at time of rescue. T-0041..T-0045 were identified in F-002 but never formally filed.

## Source

- Repo: `git@github.com:konf-dev/konf-genesis.git` (renamed from `konf-genesis.kell`)
- Branch: `main` at the point of archive
- Archive branch: `archive/vibe-coded-v1`

## Do not edit

These files are frozen evidence. Any new friction log / thread log for the fresh genesis goes somewhere else — decided when the fresh bootstrap begins.
