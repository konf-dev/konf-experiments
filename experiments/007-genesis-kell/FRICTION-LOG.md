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

---

## 2026-04-10 — gitleaks-action requires paid license for org repos
**Category:** infra-gap
**Phase:** 0.6
**Severity:** medium (CI-blocking until fixed)

### What happened
The Phase 0.6 dashboard workflow used `gitleaks/gitleaks-action@v2` to run the CI secret scan. On the first PR (reset/v0 → main), the `security` job failed in 6 seconds with:

> `[konf-dev] is an organization. License key is required.`
> `🛑 missing gitleaks license. Go grab one at gitleaks.io and store it as a GitHub Secret named GITLEAKS_LICENSE.`

gitleaks-action changed its policy — the action now requires a paid license key to run on any organization-owned repository. Public or not, org or not, it refuses to run without `GITLEAKS_LICENSE`.

### What we tried
- Considered adding a stub license — rejected, not free for orgs.
- Considered switching to trufflehog — viable but different tool to learn.
- **Landed on: install the `gitleaks` binary directly.** The gitleaks binary itself is free and open source — only the GitHub Action wrapper is paywalled. Two `curl` + `tar` steps get it into `/usr/local/bin`, then we run `gitleaks detect --exit-code 1` with the same strictness.

### Outcome
**Fixed in place.** The workflow now installs gitleaks 8.21.2 as a binary and runs `detect` with `--report-format json`. No paid dependency. On the re-run the `security` job passed in 7 seconds and the merge went through. Publish job deployed the dashboard to Pages 11 seconds later.

### Links
- Fix commit: [`1797c94`](https://github.com/konf-dev/konf-genesis.kell/commit/1797c94) — `fix(ci): install gitleaks as binary instead of gitleaks-action`
- Workflow: [`.github/workflows/dashboard.yml`](https://github.com/konf-dev/konf-genesis.kell/blob/main/.github/workflows/dashboard.yml)
- gitleaks-action policy change: https://github.com/gitleaks/gitleaks-action#-announcement

### Lesson
GitHub Actions from third parties can flip paid at any time. Prefer installing upstream binaries directly when the tool itself is open source — the action is just glue. This is a specific instance of the doctrine "reuse over reinvention, but *only* the genuinely open parts."

---

## 2026-04-10 — Phase 0.6 deployed to GitHub Pages; branch protection active
**Category:** surprise (positive)
**Phase:** 0.6
**Severity:** n/a (milestone)

### What happened
Phase 0.6 landed on `main` via PR #1 (squash-merged). The dashboard deployed to https://konf-dev.github.io/konf-genesis.kell/ in under 12 seconds from merge. Branch protection applied cleanly to `main` with the full rule set from `.github/branch-protection.json`.

### What we tried
- Opened PR #1 (reset/v0 → main) with the full Phase 0 + Phase 0.6 scope.
- First CI run failed (gitleaks-action license — see entry above). Fixed by switching to binary install.
- Second CI run passed in 7 seconds. Squash-merged, `reset/v0` branch deleted.
- Publish job ran automatically on the merge-to-main push, deploying the dashboard to Pages.
- `gh api -X PUT branches/main/protection` applied the protection rules. Verified every field.

### Outcome
**Done.** From this moment, every change to `main` in `konf-genesis.kell` requires a PR + CI green + CODEOWNERS review + signed commits + linear history, with admins included (no bypass for bert either). The dashboard is live at https://konf-dev.github.io/konf-genesis.kell/ with the boot animation, ori's etymology, and the CSP-locked static page.

### Known deferred issues
- `operator/prompts/broker.md` link on the dashboard returns 404 — Phase 0.5 deliverable. Will resolve when Phase 0.5 lands.
- `konf-agents[bot]` not yet installed on the kell repo — defer until Phase 1 when the Broker actually needs write access.
- `konf.dev/genesis` redirect — deferred until bert updates konf.dev.

### Links
- PR #1: https://github.com/konf-dev/konf-genesis.kell/pull/1
- Merge commit: [`26113a6`](https://github.com/konf-dev/konf-genesis.kell/commit/26113a6)
- Live dashboard: https://konf-dev.github.io/konf-genesis.kell/
- Branch protection spec: [`.github/branch-protection.json`](https://github.com/konf-dev/konf-genesis.kell/blob/main/.github/branch-protection.json)
- Workflow runs: https://github.com/konf-dev/konf-genesis.kell/actions

### Lesson
The whole "Phase 0.6 going public" loop — identity + privacy + dashboard + CI gate + branch protection — took roughly one working session. Doctrine held: no new Rust, everything expressed as YAML/markdown/compose/config. Kernel minimalism worked.
