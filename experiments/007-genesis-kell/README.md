# Experiment 007: `genesis.kell`

**Status:** Reset — rebuilding from first principles on branch `reset/v0`
**Date:** 2026-04-10 (reset)
**Repo:** [`konf-genesis.kell`](https://github.com/konf-dev/konf-genesis.kell)
**Plan:** `~/.claude/plans/nifty-sniffing-cake.md`

## Reframing

The earlier framing of genesis as a "Second-in-Command sandbox product" with `spawn_kell` / `system_pulse` workflows, an LiteLLM+Ollama+OpenCode-Zen power stack, and Infisical integration — all of it drifted from doctrine. It was speculative architecture ahead of experimental friction.

**New framing (2026-04-10):** genesis is a **bootstrap proof**, nothing more and nothing less.

- Kell #1 is a frozen bundle of YAML and prompts.
- It runs on a Hetzner VPS with Podman + Tailscale.
- Inside it runs a Broker agent — OpenCode for v0, doctrine-bound, curious.
- From Bert's laptop, over Tailscale, he can drive the kell via Claude Code (MCP) or directly via OpenCode (TUI over SSH).
- The Broker's job is to help build kell #2, kell #3, the CI pipeline, the public platform, and the startup around it.

**What this experiment proves:** that kell #1 can exist, run reproducibly, and be useful enough to bootstrap the rest of the ecosystem from inside itself. If it boots and is useful, the thesis survives.

## Non-goals (what was in the old plan and is now explicitly out)

- `spawn_kell` meta-workflow — deferred until kell #2 creates the friction
- LiteLLM multi-provider routing — OpenCode picks a model
- Infisical SDK integration — env files + file-mounted PEMs for v0
- Published `ghcr.io/konf-dev/kernel` image — build locally on the VPS
- Replacing OpenCode with a konf-native agent — later experiment
- Event bus, dynamic agent lifecycle — Phase F2/F3 roadmap, not here

## Architecture (v0)

```
[Bert's laptop]
  ├── Claude Code (me)  ──MCP──┐
  └── OpenCode client (opt.)   │
                               │ (Tailscale mesh, no public ports)
                               ▼
[Hetzner VPS: genesis-01]
  ├── Tailscale (identity)
  ├── Podman (rootless)
  └── genesis.kell (podman-compose):
      ├── opencode   ← the Broker agent (v0 runtime)
      ├── konf       ← MCP server exposing tool suite
      ├── postgres   ← konf runtime journal
      └── workspace  ← bind-mounted kell repo
```

**Invariant across runtime changes:** the Broker persona (`konf-genesis.kell/operator/prompts/broker.md`) is the same prompt, doctrine, and tool contract. Today the Broker runs inside OpenCode. Tomorrow it runs inside a konf-native loop. The persona outlives every specific runtime.

## Phase plan

| Phase | State | Scope |
|---|---|---|
| 0 — reset + spec + static config | **in progress** | docs + YAML, no infra |
| 0.5 — Broker persona as publishable artifact | pending | `broker.md`, `docs/broker.md`, OpenCode wiring draft |
| 1 — local dry-run on laptop | pending | `compose.yml`, `justfile`, smoke tests |
| 2 — Hetzner VPS bootstrap | pending | `bootstrap/vps.sh`, systemd, tailnet |
| 3 — remote bridge (MCP over Tailscale) | pending | Claude Code + OpenCode remote control verified |
| 4 — first useful work | pending | Broker completes a real task, friction log populated |
| 5 — freeze + handoff | pending | `FREEZE.md`, reproducibility contract |

Each phase has explicit entry and exit gates — see the plan file.

## Artifacts

- [`konf-genesis.kell`](https://github.com/konf-dev/konf-genesis.kell) — the kell repo (canonical source of truth)
- [`FRICTION-LOG.md`](./FRICTION-LOG.md) — every surprise, every deferral, every kernel gap
- `~/.claude/plans/nifty-sniffing-cake.md` — the full phase-by-phase execution plan
- `konf/docs/architecture/design-principles.md` — root doctrine

## What the old framing got right (and is preserved)

- Podman + Tailscale for the host layer — still the right tools
- Tailnet-only MCP endpoint — still the right security model
- Reuse over reinvention — still the principle
- A Broker role distinct from Bert's role — preserved and made runtime-independent

## What the old framing got wrong (and is removed)

- Speculative workflows (`spawn_kell`, `system_pulse`) committed as code before they had friction to prove they were needed
- A "power LLM stack" design before we'd felt a single provider's limits
- Two sources of truth (`konf-genesis.kell` + `konf/products/genesis/`) with duplicated config
- Infisical-in-compose before any secret had actually needed rotating
- "Second-in-Command" framing that oversold what genesis actually was — kell #1, not a feature product
