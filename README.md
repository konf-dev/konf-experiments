# Konf Experiments

Findings, briefs, and bootstrap config for the [Konf Agent OS](https://github.com/konf-dev/konf).

## What's here

- **`findings/`** — Empirically discovered friction points from running konf against real workloads. Each file has a `status` frontmatter (`RESOLVED`, `VALIDATED`, `BY-DESIGN`, `DROPPED`, or `OPEN`). Status matrix in [`FINDINGS-RESOLUTION.md`](FINDINGS-RESOLUTION.md).
- **`briefs/`** — Active work items (the pipeline that turns findings into code changes).
- **`templates/issue-brief.md`** — template for new briefs.
- **`bootstrap/.mcp.json`** — configures the `konf` and `genesis` MCP servers for this workspace. `bootstrap/CLAUDE.md` is the agent instruction file (gitignored, local only).

## Running against konf

`bootstrap/.mcp.json` exposes two konf MCP servers:

- **`konf`** — points at `../konf/products/devkit/config` (canonical reference product)
- **`genesis`** — points at `../konf-genesis/kell/config` (local dev product, requires a running Postgres)

Build konf first:

```bash
cd ../konf
cargo build --release --bin konf-mcp
```

Then restart your MCP client (Claude Code, Claude Desktop, Cursor). The `konf` and `genesis` tools will appear in the tool list.

## Single source of truth

See [`../konf/docs/MENTAL_MODEL.md`](../konf/docs/MENTAL_MODEL.md) for konf's architecture, vocabulary, and doctrine. This repo's content follows it.
