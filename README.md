# Konf Experiments

Experiment logs, findings, and roadmap for the [Konf Agent OS](https://github.com/konf-dev/konf).

## What's here

- **`findings/`** — Empirically discovered friction points from running experiments. Each file has a `status` frontmatter (`RESOLVED`, `VALIDATED`, `BY-DESIGN`, `DROPPED`, or `OPEN`). Status matrix in [`FINDINGS-RESOLUTION.md`](FINDINGS-RESOLUTION.md).
- **`experiments/`** — Individual experiment logs with results:
  - `001-architect/` — architect agent builds a product via MCP (proven)
  - `003-dev-environment/` — VCS agent identity, agentic tool-calling, nested composition (proven)
  - `004-autonomous/` — autonomous agents via the `schedule` primitive (6/8 criteria passed)
  - `005-init-system/` — init product design
  - `007-genesis-kell/` — bootstrap proof for a standalone product. Contains current-state README + friction log + the `vibe-coded-v1/` post-mortem of the previous attempt. **Read `vibe-coded-v1/friction-log.md` before starting any new product work** — the ten drifts it documents are load-bearing lessons.
- **`briefs/`** — Active work items (the pipeline that turns findings into code changes).
- **`templates/issue-brief.md`** — template for new briefs.
- **`bootstrap/.mcp.json`** — configures the `konf` and `genesis` MCP servers for this workspace. `bootstrap/CLAUDE.md` is the agent instruction file (gitignored, local only).
- **`experiments/STATUS-2026-04-09.md`** — current platform status.

## Running experiments

Experiments run against the MCP servers configured in `bootstrap/.mcp.json`:

- **`konf`** — points at `../konf/products/devkit/config` (canonical reference product)
- **`genesis`** — points at `../konf-genesis/kell/config` (local dev product, requires a running Postgres)

Build konf first:

```bash
cd ../konf
cargo build --release --bin konf-mcp
```

Then restart your MCP client (Claude Code, Claude Desktop, Cursor). The `konf` and `genesis` tools will appear in the tool list.

## Single source of truth

See [`../konf/docs/MENTAL_MODEL.md`](../konf/docs/MENTAL_MODEL.md) for konf's architecture, vocabulary, and doctrine. This repo's docs follow it.
