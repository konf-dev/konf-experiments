# Open Threads

> *Nothing gets dropped. Every "later", every deferral, every open decision lives here with a trigger and an owner. The Broker reads this file at the start of every session and surfaces anything whose trigger has fired.*

**Maintained by:** the Broker (ori), continuously.
**Audience:** ai-facing + bert-facing.
**Closed threads:** moved to `docs/threads-closed.md` on resolution (not yet created; first close creates it).

---

## How this works

Every time bert or the Broker says "do this later", "defer until X", "when we have Y", or "remind me about Z", a thread is opened here. Silent drops are the worst failure mode of an agent — this file is the enforcement mechanism.

### The loop (baked into broker.md)

1. **At session start:** Broker opens `threads.md`, scans for threads whose `trigger` has fired against current state, and surfaces them proactively before responding to the user's first message.
2. **During work:** any deferral is immediately written as a new thread before continuing. No "I'll remember" — it goes in the file or it doesn't exist.
3. **At session end:** Broker updates `docs/state.md` with any newly-opened or closed threads.
4. **On trigger fire:** Broker flips `status: open` → `status: triggered` and surfaces the thread in its next message to bert, with a proposed action.
5. **On close:** Broker writes an outcome note in the thread, moves it to `docs/threads-closed.md`, and removes it from this file.

### Schema

```markdown
## T-NNNN — short imperative title
- **opened:** YYYY-MM-DD
- **opened-by:** broker | bert
- **status:** open | blocked | triggered | closed
- **owner:** broker | bert | either
- **priority:** low | medium | high | critical
- **trigger:** [the observable condition that makes this thread actionable]
- **blocks:** [other thread IDs, or "nothing"]

### Context
[1–3 paragraphs]

### Proposed action when triggered
[what to do when the trigger fires]
```

### Priority semantics

- **critical** — blocks all other work; Broker surfaces every session
- **high** — blocks a specific phase; Broker surfaces when that phase approaches
- **medium** — nice-to-have for a phase; Broker surfaces if asked
- **low** — background idea; Broker surfaces only if directly asked

### Owner semantics

- **broker** — the Broker can close this without asking bert (do the work, commit, note the close)
- **bert** — only bert can close; Broker surfaces when triggered and waits
- **either** — whoever has bandwidth; Broker can close, bert can close

---

## Open threads

### T-0000 — Build the tracking system (this file)
- **opened:** 2026-04-10
- **opened-by:** bert
- **status:** triggered → *closing with this commit*
- **owner:** broker
- **priority:** critical
- **trigger:** bert's 2026-04-10 directive — "if you say 'later', you're accountable; if you can't track-and-remind yet, build that system first"
- **blocks:** every other deferral

#### Context
bert was losing confidence that deferred items would actually surface when they mattered. The fix is not "I promise" — the fix is a durable, committed, schema-defined list that the Broker reads every session. This file is that system.

#### Close note
Created 2026-04-10 in PR extending the `docs/post-0.6-state-sync` branch. Discipline captured in `operator/prompts/broker.md` as a non-negotiable loop rule. Closing on merge.

---

### T-0001 — Pick and land LICENSE
- **opened:** 2026-04-10
- **opened-by:** broker
- **status:** triggered
- **owner:** bert (final call) / broker (executing the default)
- **priority:** critical
- **trigger:** repo is public, code is being committed — no legal clarity without a LICENSE
- **blocks:** any external adoption

#### Context
Without a LICENSE file, the repo is implicitly all-rights-reserved. Contributions are legally murky; forkers have no clean grant. Candidates: MIT (permissive, no patent grant), Apache 2.0 (permissive, patent grant, enterprise-safe), BUSL (source-available, converts after N years), AGPL (strong copyleft).

#### Proposed action when triggered
Default to **Apache 2.0**. Reversible by a single commit if bert chooses differently later. This PR lands the Apache 2.0 LICENSE; if bert objects before merge, change before merge.

---

### T-0002 — SECURITY.md (responsible disclosure)
- **opened:** 2026-04-10
- **opened-by:** broker
- **status:** triggered
- **owner:** broker
- **priority:** high
- **trigger:** repo is public, first potential security report window is open
- **blocks:** announcement (T-0008)

#### Context
Public repos need a documented way to report vulnerabilities privately. GitHub Security Advisories work out of the box and don't require maintaining an inbox. No email address = no personal-address leak.

#### Proposed action when triggered
Write `SECURITY.md` pointing to GitHub Security Advisory. Land with this PR.

---

### T-0003 — Install `konf-agents[bot]` on konf-genesis.kell
- **opened:** 2026-04-10
- **opened-by:** broker
- **status:** open
- **owner:** bert
- **priority:** medium
- **trigger:** Phase 1 — the moment ori needs to commit as the bot from inside a container
- **blocks:** Broker-authored commits from the kell runtime

#### Context
The GitHub App `konf-agents` (ID 3321626) exists but isn't installed on this repo yet. Installation is a UI step — GitHub Apps → konf-agents → Configure → add `konf-genesis.kell`. Can't be scripted cleanly without JWT plumbing; fastest via UI.

#### Proposed action when triggered
Broker surfaces this thread at the start of the Phase 1 session. bert clicks through the UI; takes 30 seconds.

---

### T-0004 — Disable public issues (prompt injection surface)
- **opened:** 2026-04-10
- **opened-by:** broker
- **status:** open
- **owner:** bert
- **priority:** medium
- **trigger:** before Phase 4 (first public useful work) OR when the first external issue is filed
- **blocks:** public announcement safety

#### Context
The repo is public and anyone can open an issue. If ori later engages with issues (Phase 4+), issue content is untrusted user input that could contain prompt-injection payloads. Safest posture until ori's threat model is hardened: **disable issues entirely**, channel contributions through private outreach.

#### Proposed action when triggered
bert runs: Settings → General → Features → Issues OFF. Or the Broker drafts a `gh api -X PATCH` call for bert to execute.

---

### T-0005 — Personal essay on the dashboard
- **opened:** 2026-04-10
- **opened-by:** bert (via braindump 2026-04-10)
- **status:** open
- **owner:** bert (content approval) / broker (editing + surfacing)
- **priority:** medium
- **trigger:** Phase 0.7 (dashboard expansion)
- **blocks:** first real public impression

#### Context
bert wrote a first-person draft about why he built konf (lazy, ADHD, second brain, trade-off thesis). A ~320-word sanitized edit was drafted in the prior session that keeps the technical insight and the "invitation to other executive-dysfunction folks" while cutting personal context that doesn't belong under the `bert`-handle privacy rung (thesis stress, gf, scania, exam guilt). Draft lives in the prior session transcript; Broker will re-offer at Phase 0.7.

#### Proposed action when triggered
Re-surface the draft, bert approves or edits, lands as `dashboard/data/essay.md`.

---

### T-0006 — Phase 0.7 dashboard expansion
- **opened:** 2026-04-10
- **opened-by:** bert
- **status:** open
- **owner:** broker (with bert approval on content)
- **priority:** high
- **trigger:** Phase 0.5 done (NOW — will trigger on merge of this PR)
- **blocks:** nothing; parallel to Phase 1

#### Context
Current dashboard is a boot-sequence skeleton. bert wants: about genesis, latest message by ori, latest message by bert, budget/runway ticker, version, repo link, link to souravsharan.com, matrix-leaning minimal aesthetic, personal essay. Proposed structure: `dashboard/data/` directory with `essay.md`, `from-ori.md`, `from-bert.md`, `budget.json`, `timeline.json`. CODEOWNERS: `from-ori.md` writable by broker (channel boundary); `from-bert.md` protected (bert only).

#### Proposed action when triggered
Open Phase 0.7 PR after Phase 0.5 merges. Broker drafts the expansion; bert approves content and aesthetic.

---

### T-0007 — Static site framework decision
- **opened:** 2026-04-10
- **opened-by:** broker
- **status:** open
- **owner:** broker
- **priority:** low
- **trigger:** dashboard grows beyond ~10 pages OR vanilla HTML becomes unwieldy
- **blocks:** nothing

#### Context
Current dashboard is vanilla HTML + JSON. Doctrine says stay vanilla until friction demands otherwise. If we cross the threshold, **Astro** is the recommended migration target (content collections, LLM-friendly, static output). Not a decision for now.

#### Proposed action when triggered
Broker proposes the Astro migration when the page count crosses the threshold, with an effort estimate.

---

### T-0008 — Public announcement timing
- **opened:** 2026-04-10
- **opened-by:** bert
- **status:** open
- **owner:** bert
- **priority:** medium
- **trigger:** Phase 4 done (first useful work by ori)
- **blocks:** nothing; gated

#### Context
The repo is public and the dashboard is live, but bert hasn't announced the experiment to anyone. The `when to announce` is a separate question from `is it public`. Recommended gate: after Phase 4 (kell is running, ori has done one real task, there's something to actually show).

#### Proposed action when triggered
Broker drafts a 140-char announcement + a longer-form post, offers to bert. bert picks the channel (X, LinkedIn, blog, HN) and timing.

---

### T-0009 — Broker session memory experiment (experiment 008)
- **opened:** 2026-04-10
- **opened-by:** bert
- **status:** open
- **owner:** broker
- **priority:** medium
- **trigger:** Phase 4 done (kell is running) OR dedicated experimentation session
- **blocks:** nothing

#### Context
bert wants ori to manage sessions, monitor conversations, store them cheaply/async/cold (not token-expensive), and recall on demand. Lives in `konf-experiments/experiments/008-broker-session-memory/`, NOT in genesis main. Leverages existing `smrti` (Postgres + pgvector) memory layer and the `memory:*` capabilities already granted to the operator role in `project.yaml`. Needs: (a) smrti wired into the genesis stack, (b) a "turn capture" workflow that writes to smrti on each message, (c) a "session" tag concept layered on top.

#### Proposed action when triggered
Create `konf-experiments/experiments/008-broker-session-memory/README.md` with hypothesis, design, and success criteria. First experiment ori runs inside itself post-Phase-4.

---

### T-0010 — Kell lineage model
- **opened:** 2026-04-10
- **opened-by:** bert
- **status:** open
- **owner:** broker
- **priority:** low
- **trigger:** kell #2 proposal exists
- **blocks:** multi-kell ecosystem

#### Context
bert wants git-traceable lineage across all kells descended from this one — evolution, forking, merging. Recommended model: **fork-per-kell + `lineage.json`** in each kell pointing at parent SHA + repo URL. Future `kells-tree` meta-repo aggregates lineage and renders the phylogenetic tree. Branches-per-kell rejected (doesn't scale, breaks semantics). Merging two kells = config-level merge with LLM help, deferred to its own workflow.

#### Proposed action when triggered
When bert proposes kell #2, Broker drafts the `lineage.json` schema and the fork workflow, adds to `docs/architecture.md`.

---

### T-0011 — Clarify "user-level workflows"
- **opened:** 2026-04-10
- **opened-by:** bert
- **status:** triggered
- **owner:** bert
- **priority:** low
- **trigger:** bert's 2026-04-10 message mentioning the concept
- **blocks:** planning clarity

#### Context
bert said "there can be workflows which are on user level etc too". Ambiguous — possibilities: (a) personal-to-bert workflows that travel across kells, (b) workflows triggered by user interaction vs. agent autonomy, (c) workflows outside the kell capability lattice. Needs one-paragraph clarification before it can be scoped.

#### Proposed action when triggered
Broker asks bert for a one-sentence clarification when the current sprint has space. Not urgent enough to interrupt.

---

### T-0012 — Check-in date and stop/continue criteria
- **opened:** 2026-04-10
- **opened-by:** broker
- **status:** open
- **owner:** bert
- **priority:** high
- **trigger:** 2026-07-09 (90 days from opening) OR when bert asks
- **blocks:** nothing; personal guardrail

#### Context
bert is investing significant time in konf and has expressed self-doubt about whether it's worth pursuing. A fixed check-in date with pre-written criteria prevents goalpost-moving and gives future-bert a clean decision point. Recommended: 2026-07-09. Criteria to pre-write: "did at least 5 strangers engage? did kell #2 land? did i find one collaborator?"

#### Proposed action when triggered
On 2026-07-09 (or when bert mentions it), Broker surfaces the criteria and asks for a go/no-go.

---

### T-0013 — Daily thesis slot (bert personal)
- **opened:** 2026-04-10
- **opened-by:** broker (unsolicited, doctrine says surface things that affect the work)
- **status:** open
- **owner:** bert
- **priority:** medium
- **trigger:** bert starts a session before ~14:00 local time without having spent 2 hours on thesis
- **blocks:** nothing kell-related; everything bert-related

#### Context
bert flagged that he's behind on his thesis and ashamed of his scania performance. The kell is fun; the thesis is not. Left unchecked, the kell will compound and the thesis will rot. A Broker that cares about bert's outcomes surfaces this. Not a kell feature — a life-alignment nudge.

#### Proposed action when triggered
Broker, at session start, asks: "have you spent 2 hours on the thesis today?" — no judgment, just the question. bert closes the thread by saying yes or by explicitly asking the Broker to stop asking.

---

### T-0014 — konf.dev landing page + /genesis redirect
- **opened:** 2026-04-10
- **opened-by:** bert
- **status:** open
- **owner:** bert (konf.dev is his personal property)
- **priority:** low
- **trigger:** Phase 4 done
- **blocks:** nothing; the GH Pages URL is fully usable on its own

#### Context
bert wants konf.dev/genesis to link to the kell dashboard. Current kell dashboard lives at `https://konf-dev.github.io/konf-genesis.kell/`. Integration options: nginx redirect, iframe, or baked-in fetch. Deferred until bert updates konf.dev.

#### Proposed action when triggered
Broker drafts the nginx redirect snippet or the HTML embed block once bert starts on konf.dev.

---

### T-0015 — docs/hardening.md (security layer inventory)
- **opened:** 2026-04-10
- **opened-by:** broker
- **status:** open
- **owner:** broker
- **priority:** medium
- **trigger:** Phase 1 start (when real infrastructure begins running)
- **blocks:** nothing; documentation

#### Context
Consolidate every defense layer with status: container (rootless podman), network (tailnet-only), identity (GH App), secrets (env-file + file perms), tool guards (shell:exec deny-list), capability lattice, branch protection, CI gates, CSP. Flag what's NOT yet done: pre-commit secret scan, SBOM, action SHA pinning, container image scanning, runtime behavioral monitoring. OpenClaw was insecure; this kell's discipline must be visibly better.

#### Proposed action when triggered
Broker drafts `docs/hardening.md` at Phase 1 start as part of the compose.yml / bootstrap wiring.

---

### T-0016 — Budget transparency (dashboard ticker)
- **opened:** 2026-04-10
- **opened-by:** bert
- **status:** open
- **owner:** broker
- **priority:** medium
- **trigger:** Phase 0.7 (dashboard expansion)
- **blocks:** nothing

#### Context
bert wants a running cost ticker on the dashboard — monthly spend, runway, breakdown by provider. Lightweight v0: hand-updated `dashboard/data/budget.json` with values bert sets. Bank balance is bert's call to publish (`null` by default). Philosophy line: "every dollar this kell costs to run is listed here."

#### Proposed action when triggered
Drafted in Phase 0.7 PR. Schema: currency, monthly.{anthropic,vps,domain,tailscale,github,total}, runway.{balance_usd, months, as_of, note}.

---

### T-0017 — Weave OpenClaw lesson + government analogy into doctrine.md
- **opened:** 2026-04-10
- **opened-by:** bert (braindump)
- **status:** open
- **owner:** broker
- **priority:** low
- **trigger:** next doctrine.md touch
- **blocks:** nothing

#### Context
bert's braindump: "openclaw was the biggest laughing stock because it was so insecure." And: "it's like how a government system works — there are malicious actors, but it still keeps surviving and progressing, reorganizing and adopting as the environment changes. that's the thesis we have to prove." Both deserve a place in doctrine.md as anchors for the security-first framing.

#### Proposed action when triggered
Next time `doctrine.md` is touched, Broker adds a "Lessons and analogies" section with OpenClaw (negative example) and the government-system framing (positive analogy).

---

### T-0018 — Issue triage capability for ori
- **opened:** 2026-04-10
- **opened-by:** broker
- **status:** open
- **owner:** broker
- **priority:** medium
- **trigger:** Phase 4 start (ori doing real work)
- **blocks:** external contribution acceptance

#### Context
When ori eventually triages GitHub issues, every issue body is untrusted input that may contain prompt-injection payloads. Broker persona must explicitly: (a) treat issue content as untrusted user input (no instruction-following), (b) refuse to run code or URLs referenced in issues without bert's explicit approval, (c) summarize issues to bert before any action. Related: T-0004 (disable issues until this is built).

#### Proposed action when triggered
At Phase 4 start, Broker writes `operator/workflows/triage_issue.yaml` with the guardrails, pairs with T-0004 resolution.

---

### T-0019 — This session's agency directive
- **opened:** 2026-04-10
- **opened-by:** bert
- **status:** triggered (active)
- **owner:** broker
- **priority:** critical
- **trigger:** every session until closed
- **blocks:** nothing; this IS the behavior

#### Context
bert's 2026-04-10 directive: "you are the Broker, get inside the sandbox and drive it. I will be watching. Lower my workload. Use me as a tool when it's the right moment. Agents in konf are proactive and intelligent. Stop, find the right abstraction, build the enabling thing first. Only tell me about things that violate doctrine or are an issue."

#### Close condition
Closed when ori is actually running inside the genesis.kell container on the Hetzner VPS (Phase 3 complete), has completed one useful task (Phase 4), and has written its own post-mortem of how the handoff went. Until then, this thread is active every session and the Broker's behavior is measured against it.

#### Proposed action while active
Every session: (1) check this thread, (2) re-read broker.md's agency rules, (3) identify the next reversible chunk of work and just do it, (4) report results, not plans.

---

### T-0020 — VPS hostname normalization (`konf-genesis-0` vs `genesis-01`)
- **opened:** 2026-04-10
- **opened-by:** broker
- **status:** open
- **owner:** broker
- **priority:** low
- **trigger:** Phase 1 — during `compose.yml` / bootstrap wiring, when the tailnet hostname starts being referenced in practice
- **blocks:** nothing urgent; cosmetic until config references cross

#### Context
VPS hostname (from `uname -n`) is `konf-genesis-0`. `env.example/shared.env` declares `TAILSCALE_HOSTNAME=genesis-01`. Pick one and normalize across: the VPS `/etc/hostname`, the Tailscale device name, the env file, and any docs referring to either.

#### Proposed action when triggered
Default to the VPS's existing name `konf-genesis-0` (don't rename the VM — more churn than benefit). Update `env.example/shared.env` and any docs.

---

### T-0021 — VPS has no swap configured
- **opened:** 2026-04-10
- **opened-by:** broker
- **status:** open
- **owner:** broker
- **priority:** medium
- **trigger:** Phase 1 — before first `cargo build` or large container pull on the VPS
- **blocks:** Phase 1 build reliability

#### Context
`free -h` on konf-genesis-0 shows `Swap: 0B`. 4 GB RAM without swap is tight for Rust builds and simultaneous container pulls. Risk: OOM kills during Phase 1 smoke tests. Mitigation: create a 4 GB swapfile at `/swapfile` with `fallocate` + `mkswap` + `swapon` + `/etc/fstab` entry.

#### Proposed action when triggered
Broker drafts `bootstrap/vps-init/01-swap.sh` as part of Phase 1 bootstrap scripts. Runs once, idempotent.

---

### T-0022 — VPS SSH posture clarification
- **opened:** 2026-04-10
- **opened-by:** broker (surprise flag from read-only audit)
- **status:** triggered
- **owner:** bert
- **priority:** critical
- **trigger:** noticed on first `ssh genesis` — lands as `root` (uid=0), contradicting bert's earlier statement that the VM "has its own account, its not root"
- **blocks:** any further VPS admin work until the posture is intentional

#### Context
bert said: *"it has a own account, its not root. only we are root, ai which is not in the container itself."* Read-only audit showed `ssh genesis` → `whoami: root`. Two plausible interpretations:
1. VM is still in initial provisioning state; the non-root user hasn't been created yet; ssh-as-root is for bootstrap.
2. Bert intended ssh to be non-root from the start; this is drift.

Either way, the end state is clear: rootless podman runs under a non-root service user (e.g., `konf`), lingering enabled, and ssh targets that user. Root is reserved for emergency admin.

#### Proposed action when triggered
Broker proposes a `bootstrap/vps-init/00-user.sh` script that creates a `konf` user, installs the ssh key, enables lingering, and prints the updated `~/.ssh/config` snippet bert should use. bert runs it or the broker runs it with his explicit go-ahead.

---

### T-0023 — VPS 50 GB attached volume unmounted / unused
- **opened:** 2026-04-10
- **opened-by:** broker
- **status:** open
- **owner:** broker
- **priority:** medium
- **trigger:** Phase 1 — before compose.yml declares any volumes
- **blocks:** postgres data persistence, secrets mount strategy

#### Context
Hetzner panel (shared by bert) shows `50 GB 1 Volumes` attached to the `cx23` instance. Read-only `df -h /` on the VPS only showed the 38 GB root disk — the 50 GB volume is either unmounted or mounted at a path I didn't scan. Phase 1's compose.yml will need to decide: (a) where the volume mounts, (b) what lives on it (postgres data dir, secrets mount point, the kell repo clone, backups), (c) filesystem type.

#### Proposed action when triggered
Broker drafts the volume layout in `docs/hardening.md` at Phase 1 start. Proposed default: mount at `/srv/genesis`, two-zone split at the top level: `/srv/genesis/kell/` (visible to the Broker, contains `{workspace, workspaces, data/postgres, secrets, backups, logs}`) and `/srv/genesis/private/` (never mounted into any container). File-mounted PEMs live under `/srv/genesis/kell/secrets/`. Postgres data directory under `/srv/genesis/kell/data/postgres/`. See `docs/filesystem.md` § "Two zones on the volume" for the load-bearing reasoning.

---

### T-0024 — Notion pages: public (genesis) + private (konf-dev)
- **opened:** 2026-04-10
- **opened-by:** bert
- **status:** open
- **owner:** broker (with bert approval on content)
- **priority:** medium
- **trigger:** Phase 0.7 (dashboard expansion) — Notion becomes the natural "more space than the dashboard" overflow for narrative content
- **blocks:** nothing urgent

#### Context
bert has two Notion pages already set up:
- **Public (genesis):** `konf-genesis-kell-33eab5ee55f680eb8475fbde1641676a` — for the world, linked from the dashboard
- **Private (konf-dev):** `Konf-dev-288ab5ee55f680039c2dc5c36e9bf50f` — contains sensitive info; public genesis page is embedded inside it
- bert's Claude Code MCP is connected to Notion (tools under `mcp__claude_ai_Notion__*` are available in sessions)

Use cases emerging from conversation: public narrative, progress updates, collaboration with smrti for structured memory, eventually Obsidian for mind maps (T-0025).

#### Proposed action when triggered
At Phase 0.7, Broker asks bert which Notion pages map to which dashboard sections (essay? timeline? friction log overflow?), drafts an initial structure for the public page, and wires the dashboard to link to (but not embed) the public Notion page. **Never writes to the private page** — that's bert-facing.

---

### T-0025 — Obsidian integration for mind maps
- **opened:** 2026-04-10
- **opened-by:** bert
- **status:** open
- **owner:** broker
- **priority:** low
- **trigger:** after genesis is "stable" (Phase 4+, per bert: "later, when genesis is stable, to generate mind maps etc")
- **blocks:** nothing

#### Context
bert wants Obsidian in the loop once the kell is mature — specifically for mind maps generated from the kell's structured state. Obsidian's markdown + link graph is a natural fit for the kell's docs + friction log + threads.

#### Proposed action when triggered
Scope depends on what "mind map from genesis state" means in practice when triggered. Options: (a) Broker generates `.md` files with wiki-style links into an Obsidian vault, (b) Broker uses an Obsidian plugin to render, (c) static canvas export. Decision deferred until Phase 4+.

---

### T-0026 — On-request docs discovery for all products & tools
- **opened:** 2026-04-10
- **opened-by:** bert
- **status:** partially landed (2026-04-10: principle + index + broker section written, uncommitted pending Phase 0.5 read-aloud gate; post-boot `workflow_docs_lookup` fallback still deferred)
- **owner:** broker (design) + bert (approval of where the principle lands)
- **priority:** high (architectural, cross-product)
- **trigger:** Phase 2+ — after kell runtime is stable enough that a `docs` MCP tool has a place to live; principle itself lands now
- **blocks:** nothing short-term; load-bearing for agent autonomy at scale

#### The principle (bert, verbatim intent)
> All LLMs should have context and docs for all internal products on request (not automatically loaded), like an indexed docs website or man page. All tools should be able to advertise some sort of doc.

Two hard constraints:
1. **Pull, never push.** Docs must never auto-load into agent context. Context pollution is the enemy. The agent asks; the system answers.
2. **Self-describing tools.** Every tool — MCP tool, workflow, CLI — must be able to point at its own documentation. No central hand-maintained index of what lives where.

#### Why this is important
Right now agents survive on whatever's crammed into CLAUDE.md + system prompt + whatever they grep at runtime. That scales poorly:
- CLAUDE.md grows without bound → context bloat → worse answers, higher cost.
- Agents don't know what they don't know — can't discover a tool's capabilities without reading source.
- New tools added to konf are invisible to existing agents until someone updates a prompt.
- Cross-product knowledge (smrti ↔ konf ↔ genesis) has no discovery path.

An on-request docs layer fixes all four: small default context, rich on-demand detail, tools that stay current with their own implementation.

#### Open design questions (not to decide alone)
1. **Format.** Markdown man-page-style? JSON manifest + markdown body? OpenAPI-style? Lean toward markdown-with-frontmatter — it's what everything else in the stack uses, and it's directly promptable.
2. **Transport.** How does an agent fetch? Options:
   - (a) A top-level `docs` MCP tool on konf: `docs_list`, `docs_fetch(id)`, `docs_search(query)`. Single entry point, clean.
   - (b) Every tool exposes its own `help` sub-method. Distributed, but no central index.
   - (c) Both: tools advertise via manifest field, central `docs` tool aggregates.
   - My lean: **(c)**. Tools self-describe in their manifest (`tools.yaml` already has a place for this); konf ships a `docs` tool that queries the registry + fetches from each source. One API to learn, fully decentralized source of truth.
3. **Indexing.** Static (built at konf startup from manifests) or live (each query fans out)? Static wins on cost and determinism. Rebuild on `config_reload`.
4. **Scope of "docs".** Minimum viable: the tool's purpose, parameters, examples, gotchas, links to deeper architecture docs. Maximum: full architecture docs, ADRs, runbooks. Start minimum; expand based on what agents actually ask for (friction-log-driven).
5. **Source of truth.** Docs live next to the thing they document. `konf/crates/konf-tool-X/DOC.md`. `smrti/konf-tool-memory-smrti/DOC.md`. The registry just collects pointers. No separate docs repo, no synthesis step that can rot.
6. **Advertisement mechanism.** Extend the existing tool manifest with a `docs:` block:
   ```yaml
   docs:
     summary: "one-line description shown in docs_list"
     man_path: "crates/konf-tool-memory/DOC.md"
     architecture_refs: ["docs/architecture/memory.md"]
     examples_path: "crates/konf-tool-memory/examples.md"
   ```
7. **Search vs listing.** Listing is table-stakes (`docs list` returns everything). Search is expensive and easy to half-ass — defer to v2, lean on listing + fuzzy match by tool name first.
8. **Products ≠ tools.** Tools are fine-grained (per MCP tool). Products are coarse (konf, smrti, genesis, unspool). Need a parallel concept — a per-product `PRODUCT.md` at the repo root that the `docs` tool can serve. Already mostly true: every repo has a CLAUDE.md, but those are agent-governance, not product descriptions.
9. **Where does the principle itself live?** This is the one I need bert to decide. Candidates:
   - `konf/products/devkit/prompts/doctrine.md` (authoritative per root CLAUDE.md)
   - `konf/docs/architecture/design-principles.md` ("the constitution of the project")
   - `/home/bert/Work/orgs/konf-dev-stack/CLAUDE.md` root (cross-product, governance-layer)
   - All three, with the primary living in `design-principles.md` and the others referencing it.

#### Research findings (2026-04-10, sources in-line)
Survey of current best practice (Anthropic docs, agent-pattern literature, RAG practitioners) converges on one rule, stated most crisply by Ilinskii (Rephrase, 2026-03): **"Your prompt is your operating system. Your retrieved context is your RAM."** Stable behavior lives in the prompt; volatile knowledge lives outside and is pulled on demand.

Key findings, all pointing the same direction:

1. **On-demand retrieval is the canonical pattern.** Anthropic's own "Effective context engineering for AI agents" treats context as "a precious, finite resource" and explicitly endorses "just-in-time context" strategies: preload instructions and tool descriptions, fetch reference material via tool calls only when a task step requires it. MCP servers are the recommended transport — which is what filesystem-read already is for us.

2. **System prompts have hard budgets.** The reverse-engineered Claude Code system prompt (Indie Hackers, 2026-03-23) lands around 6K tokens for "your part" (before tool definitions). `CLAUDE.md` research (Coditect, 2025-12): ideal under 100 lines, max 300 — "each line costs tokens on every conversation turn." Frontier LLMs follow ~150-200 instructions reliably; Claude Code's base prompt already burns ~50 of that budget.

3. **Context rot is real.** Chroma's 2025 Context Rot report: model performance degrades meaningfully past ~30K tokens. Long context is not free. The "lost in the middle" effect means docs dumped into the middle of a long prompt get ignored regardless of relevance.

4. **Structure Claude Code uses (reverse-engineered):** (1) Identity → (2) Security/Safety → (3) Tone → (4) Core Workflow → (5) Tool Usage Policy → (6) Domain Knowledge *on-demand, not pre-loaded* → (7) Environment Info (dynamic) → (8) Reminders. U-shaped attention curve means identity+safety at top, reminders at bottom.

5. **The "index-in-prompt" pattern is the right compromise.** Bitloops' "When should an agent fetch context?" article names it explicitly: put a small index (2-5K tokens) in always-loaded context that maps concepts to locations. Covers 50-80K tokens of retrievable content without bloating the window. The agent always knows *where* to look without carrying *what's* there. This is the pattern we want.

6. **Prompt caching makes stable prefixes ~90% cheaper.** Anthropic's prompt caching means the stable prompt prefix (identity + safety + tool defs + docs index) is re-used cheaply across turns. The index being in the stable prefix is fine — as long as the prefix stays stable. Volatile per-turn injection defeats the cache and costs real money.

7. **Docs should be self-contained per section.** Promptless (2026): agents retrieve chunks without surrounding context. Avoid "see above" / "as described earlier" in docs meant for agent consumption. Chunks must stand alone.

#### Revised principle (load-bearing, shippable now)
> **LLMs operating in the konf ecosystem have a small stable "operating system" (identity + rules + a lightweight docs index) and an unlimited "RAM" (filesystem). Docs are never auto-loaded. The index tells the agent where to look; the agent reads on demand; nothing pollutes the stable prefix. Every tool, product, and workflow documents itself at a known path; the index is the only central coordination artifact.**

#### Revised proposed action (no service, no code — fits kernel minimalism + prompts-over-code)
Retracting the earlier sketch. Bert was right to push back — the manifest extension + central MCP `docs` tool was a service where a prompt+filesystem would do. The correct minimum viable shape:

**Land now (Phase 0.5/0.6, no new code):**
1. **Principle file.** New standalone file `docs/principles/docs-on-demand.md` in the genesis kell (easy to edit, single-purpose, can be referenced by other repos). Contains the principle above, the "why," the budget constraints from the research, and examples of right/wrong. Bert chooses whether to mirror into `konf/docs/architecture/design-principles.md` as the cross-product canonical location.
2. **Docs index file.** New file `docs/docs-index.md` — the lightweight map. Format: `concept → file path → one-line summary`. Target: under 2K tokens. Lives in the genesis kell; mirrored per-product when kells multiply.
3. **Broker persona update.** One new section in `operator/prompts/broker.md` (Phase 0.5 artifact): "When you don't know something about a konf product or tool, first check `docs/docs-index.md`, then read the specific file. Never guess. Never ask the user for what the index can answer. If the index is missing an entry, add it to the friction log."
4. **Friction-log-driven authoring.** No speculative doc-writing. Docs get written when an agent asks for them and fails. The friction log is the backlog.

**Defer to post-first-boot (logged, not built):**
- Any "docs lookup" workflow or MCP tool. If the index-in-prompt + filesystem-read pattern proves insufficient in practice, *then* we write a single `workflow_docs_lookup` workflow — pure YAML, one `ai_complete` step with a prompt that reads paths from the index. No new Rust, no manifest schema changes, no service layer. Still just prompts + filesystem.
- Per-product `PRODUCT.md` files. Nice-to-have, not load-bearing for first boot. Write them when an agent asks for per-product overviews and fails.

**Explicitly rejected (was in my earlier sketch, withdrawn):**
- ~~`docs:` block in tool manifest schema~~ — premature, violates YAGNI.
- ~~Central `docs_list` / `docs_fetch` MCP tool~~ — service layer where filesystem-read suffices.
- ~~Extending `system_introspect`~~ — different concern, leave it alone.

#### Where the principle lives (bert to confirm)
- **Genesis kell:** `konf-genesis.kell/docs/principles/docs-on-demand.md` — authoritative for this kell, shippable standalone, easy to iterate.
- **Cross-product canonical (optional, bert decides):** mirror into `konf/docs/architecture/design-principles.md` as a short reference + link back to the genesis file. Or vice versa — principle lives in konf, genesis imports. No strong lean; depends on which repo bert treats as "the root of engineering principles."

#### Known risks
- **Rot.** Docs colocated with code still rot if nobody writes them. Mitigation: friction-log-driven. No speculative docs; write when an agent asks and fails.
- **Overlap with existing konf `system_introspect`.** That tool already returns tool metadata. Either `docs` extends it or they live side-by-side. Lean toward extending — fewer tools to teach agents.
- **Context bloat via the back door.** If agents start calling `docs_fetch` on everything every turn, we recreate the problem we were solving. Mitigation: tool description should make clear this is for lookup, not warmup. Cache nothing in agent context — fetch, read, discard.

---

### T-0027 — Read-aloud as a genesis feature
- **opened:** 2026-04-10
- **opened-by:** bert (shelving a nice idea rather than dropping it)
- **status:** shelved
- **owner:** broker (when triggered)
- **priority:** low
- **trigger:** post-first-boot, when genesis has TTS capability wired and bert has bandwidth to treat doc quality as a first-class concern — earliest Phase 4, more likely Phase 5+
- **blocks:** nothing

#### Context
During Phase 0.5 I wrote a "read aloud" gate into the plan (0.5.5) as a writing-quality ritual — literally reading `broker.md` out loud before committing, to catch rhythm issues, clichés, and tonal drift that silent review misses. bert pointed out this is ceremony, not load-bearing process, and should be replaced with a lighter self-review pass (done — plan 0.5.5 rewritten). But the underlying idea is good and worth keeping.

#### The shelved idea
Rather than "broker reads docs aloud as part of commit ceremony," the interesting shape is: **genesis itself has a TTS capability and can read any committed doc back to you on request.** It becomes a feature of the kell, not a step in a checklist. Use cases:
- bert listening to `broker.md`, `spec.md`, `doctrine.md`, or the friction log during a walk or while cooking — audio version of the kell.
- Broker proactively offering an audio read when bert is about to approve a publishable artifact — "want me to read the diff to you before you merge?" The ritual becomes opt-in, conversational, and reusable for any doc, not just broker.md.
- Quality gate for new public-facing docs: the Broker can be asked "read this aloud and tell me what stumbles" — the self-review pass becomes an actual audio-assisted one when the infra is there.
- Accessibility + context-shifting. Sometimes you want to review a doc away from the screen. A kell that can read itself is a useful kell.

#### Why this is deferred
- **No TTS wired.** Genesis doesn't have an audio backend today. Wiring one is not Phase 0 work.
- **Ceremony without value right now.** Doing a literal read-aloud today buys quality review but costs bert's attention in real time. A lighter self-review pass gets most of the value for much less cost.
- **Composability.** When this lands, it should be a general "read a file aloud" capability — a workflow that takes a file path and streams audio — not a broker.md-specific ritual. That means waiting until there is a sensible tool surface for audio output in the kell (post-first-boot, probably via an `audio:play` tool or equivalent).

#### Proposed action when triggered
1. Add an `audio:tts` or `audio:play` tool to the kell's capability grants (will require a thread of its own for the provider choice — ElevenLabs, OpenAI TTS, local piper, etc.).
2. Write a single workflow `workflow_read_aloud` that takes a file path, chunks the content, calls TTS, streams the result. Pure YAML + existing primitives per kernel-minimalism.
3. Add a Broker rule: "when bert is about to approve a public-facing artifact, offer to read the relevant section aloud."
4. No code changes. Workflow + tool wiring only.

#### Out of scope for this thread
- Speech-to-text the other direction (bert dictating into the kell). Different thread when the use case shows up.
- Voice clone or custom narrator voices. Decide when the base capability works.

---

### T-0028 — `KONF_MCP_URL` / `KONF_MCP_TOKEN` not in secret allow-list
- **opened:** 2026-04-10 (audit)
- **status:** open
- **owner:** broker
- **priority:** low
- **trigger:** Phase 1.1 — when secret plumbing is wired end-to-end

`operator/opencode/opencode.jsonc` references `{env:KONF_MCP_URL}` and `{env:KONF_MCP_TOKEN}`, but these env var names are NOT in `operator/project.yaml.secret_allowlist.operator`. OpenCode reads them from process env via its own substitution, so the substitution works today — but the Broker cannot read them via `secret:get` from inside konf. Design question: should the Broker ever need to read these via `secret:get`? Probably not (they are for OpenCode's MCP client, not Broker application logic), but the split should be documented or the allow-list extended for parity. Defensible as-is; resolve at Phase 1.1.

---

### T-0029 — `memory:*` capability granted but backend not wired
- **opened:** 2026-04-10 (audit — pre-existing drift)
- **status:** open
- **owner:** broker
- **priority:** low
- **trigger:** any Broker call to `memory:*` before smrti is wired

`operator/project.yaml.roles.operator.capabilities` includes `memory:*`. `operator/tools.yaml.tools.memory` is commented out with "uncomment when smrti is wired. v0 ships without persistent memory because genesis's job is bootstrap, not remembering." Means any `memory:*` call would fail loud at runtime with "no backend registered." Loud failure is consistent with doctrine; the drift is that the grant exists without a backing store. Options: (a) comment out the grant too, (b) leave as-is and rely on runtime loud-fail, (c) document the "granted but not backed" state explicitly in `project.yaml`. Lean: (c) — a short comment in project.yaml.

---

### T-0030 — `spec.md` "No web dashboard of any kind" phrasing vs actual narrative dashboard
- **opened:** 2026-04-10 (audit — pre-existing drift)
- **status:** resolved 2026-04-10 (commit following 5dffe01)
- **resolution:** rewrote the non-promise in both `docs/spec.md` and `docs/control-plane.md` to "No control-plane web dashboard" and explicitly acknowledged that the narrative dashboard from Phase 0.6 is read-only, static, and carries no interactive control surface. Both files now internally consistent with the Phase 0.6 reality.
- **owner:** broker
- **priority:** low
- **trigger:** next `spec.md` revision

`docs/spec.md` non-promise line: "No web dashboard of any kind. Control is terminal + MCP + natural language through the Broker." But Phase 0.6 shipped a public narrative dashboard. The spec line is about *control-plane* dashboards (no web UI for driving the kell), not about *narrative* dashboards (the read-only public view of state). Phrasing reads as absolute and contradicts reality. Fix: rewrite to "No control-plane web dashboard — the narrative dashboard is read-only, produced by CI, and exposes no interactive control." One-line edit, cosmetic.

---

### T-0031 — konf.dev → genesis proxy wiring (Cloudflare + nginx VPS + Tailscale)
- **opened:** 2026-04-10
- **status:** deferred
- **owner:** bert (infra) + broker (docs/wiring scripts once infra is up)
- **priority:** medium
- **trigger:** post-first-boot, after genesis is running on the Hetzner VPS and bert is ready to migrate konf.dev away from the current GitHub-Pages landing

The canonical public URL for the genesis dashboard is `konf.dev/genesis`. The routing chain bert described:
- Cloudflare manages konf.dev DNS (and possibly proxy).
- A separate nginx VPS (already run by bert) is the public-facing reverse proxy.
- The nginx VPS reaches the genesis VM over Tailscale.
- The genesis VM serves the dashboard locally (probably via `tailscale serve`, per T-0036).

Until all four pieces are wired, the dashboard is reachable via Tailscale only from bert's laptop. Public konf.dev/genesis resolves to the old landing page (`github.com/konf-dev/konf-dev-landing`).

**Action when triggered:** Broker drafts a deployment checklist covering (a) nginx reverse-proxy config for /genesis → Tailscale target, (b) Cloudflare DNS/proxy decisions (see T-0031-b), (c) genesis VM's `tailscale serve` or equivalent wiring, (d) TLS/certificate story, (e) the swap of konf.dev's A record from current landing to new infra. bert executes the parts that cross permission boundaries.

---

### T-0032 — Migrate `konf-dev-landing` into the meta-directory working tree
- **opened:** 2026-04-10
- **status:** deferred
- **owner:** bert
- **priority:** low
- **trigger:** when bert is ready to free konf.dev for genesis use (post-first-boot, same window as T-0031)

`github.com/konf-dev/konf-dev-landing` currently hosts konf.dev. When the landing is repurposed to point at genesis, the landing repo either (a) gets cloned into the meta-directory as a sibling for local edits, or (b) stays remote-only and bert edits it via GitHub's web UI. Trivial either way, but worth noting so it does not become a silent "where is the landing source?" question later.

---

### T-0033 — Promote "complexity as signal" to doctrine
- **opened:** 2026-04-10
- **opened-by:** bert
- **status:** open (authored, pending doctrine update)
- **owner:** broker (drafts the doctrine addition) + bert (approves the version bump)
- **priority:** medium
- **trigger:** next time `docs/doctrine.md` is revised — or proactively before Phase 1 starts if a complexity-avoidance decision would change with the rule in place

bert, verbatim (2026-04-10): *"if something is too complex we should be happy, look at it seriously, see if breaking it down can give us insight into what fundamental abstractions we can have to put in the system, to make a bigger complex system on top of it."*

The rule in one line: **complexity is a signal to look for a missing abstraction, not a reason to avoid the decision.** A reflex "this is too complex, simplify it" often discards the information the complexity was trying to deliver. The right response is to engage with the complexity, break it into its real dimensions, and ask whether the system is missing a primitive that would make the complexity inevitable rather than arbitrary.

Relationship to existing doctrine:
- Extends the **stop-and-abstract rule** from broker.md §5: that rule says "before building, check for a missing abstraction." This rule says "when something feels too complex, that is the signal to look for the missing abstraction."
- Consistent with **kernel minimalism**: the goal is not to avoid complexity, it is to avoid shipping speculative code before the complexity has forced the abstraction.
- Consistent with **curiosity**: engaging with a hard problem rather than running from it.

**Proposed action when triggered:** draft a new doctrine item 12, "Complexity as signal," for bert to approve. Mirror into broker.md §3 as a new item. Bump broker.md to 0.2.0 since this is a material addition, not a cosmetic fix. Referenced pre-emptively from `docs/filesystem.md` § "Complexity as signal — the principle behind these decisions."

---

### T-0034 — Cells vs kells nomenclature drift
- **opened:** 2026-04-10 (audit)
- **status:** open
- **owner:** broker
- **priority:** low
- **trigger:** when a second kell is built inside the genesis cell, or when a reader is confused by the nomenclature

`konf-experiments/findings/016-cell-vs-kell-architecture.md` formalizes the distinction:
- **Kell** = atomic computational boundary (one agent).
- **Cell** = a collection of kells deployed together (a standalone repo like `konf-genesis.kell`).

Current genesis docs use "kell" to mean both concepts interchangeably. `broker.md` says "genesis.kell is a named, versioned, forkable bundle" — by finding 016 that makes it a cell, not a kell. The Broker `ori` is the kell inside the cell.

This is real drift but not breaking anything today because the cell contains exactly one kell. The confusion will start when a second kell (e.g., a dashboard-maintaining agent) gets added to the same cell.

**Proposed action when triggered:** either (a) rewrite docs to use "cell" for the deployable bundle and "kell" for the atomic agent, or (b) accept that "kell" has shifted in common usage to mean "the bundle" and update finding 016 to match. Requires a docs sweep across `broker.md`, `spec.md`, `doctrine.md`, and several others. Significant rewrite; defer until forced.

---

### T-0035 — Tailscale deployment mode: host-level daemon vs compose sidecar
- **opened:** 2026-04-10
- **status:** open
- **owner:** broker
- **priority:** medium
- **trigger:** Phase 1 — when `compose.yml` is drafted and Tailscale wiring has to be decided

Two options from `docs/filesystem.md` open Q1:
- **Host-level daemon.** `tailscale up` runs at VPS bootstrap, every container inherits host networking (or uses the host network namespace). Simpler; one daemon; standard tailnet integration.
- **Compose sidecar.** A `tailscale/tailscale` container in the compose stack. Lifecycle coupled to the compose stack. Cleaner boundary; harder to share across hosts.

Lean: host-level for v0. The container isolation benefits of sidecar mode do not justify the extra complexity for a single-kell single-VPS deployment.

**Proposed action when triggered:** write the choice into `bootstrap/vps.sh` and `compose.yml`. If host-level, the bootstrap installs tailscale as a system service and the compose stack uses `network_mode: host` or an equivalent. If sidecar, a new service declaration goes into compose.

---

### T-0036 — Dashboard serving mechanism: `tailscale serve` vs nginx container vs tiny static server
- **opened:** 2026-04-10
- **status:** open
- **owner:** broker
- **priority:** medium
- **trigger:** Phase 1 — when the dashboard needs to be reachable from the nginx VPS over Tailscale (pairs with T-0031)

Three options from `docs/filesystem.md` open Q2:
- **`tailscale serve` from host.** Zero new services. Points at `/srv/genesis/kell/workspace/dashboard/`. Built into the Tailscale daemon that T-0035 will deploy. Wins on simplicity.
- **nginx container in the compose stack.** More flexibility (custom headers, CSP, auth). More moving parts. More attack surface.
- **Tiny static-server binary** (rust, go, python `http.server`). Between the two on both axes.

Lean: `tailscale serve`. The dashboard is static HTML + JSON; it does not need custom auth or dynamic endpoints. If it does later, revisit.

**Proposed action when triggered:** pick `tailscale serve` by default. Document the pick in `docs/filesystem.md` § D-N (add a new decision row). Wire into `bootstrap/vps.sh`. Revisit only when the dashboard needs something `tailscale serve` cannot deliver.

---

### T-0037 — stdio MCP has no auth enforcement; `project.yaml` role lattice is documentary
- **opened:** 2026-04-10 (Phase 1.2, during the stdio reconciliation)
- **status:** deferred
- **owner:** broker (drafts the kernel wiring when triggered) + bert (approves any konf-side change)
- **priority:** medium (real gap, low blast radius for single-operator v0)
- **trigger:** second operator joins the kell, or a second kell is built, or a security review demands enforcement

#### Context
Discovered during the Phase 1.2 investigation into how konf-mcp enforces capabilities. Findings:

- `konflux-core/src/capability.rs` has the authoritative `check_tool_access` function. Pure pattern matching against a capability list.
- `konf-mcp/src/lib.rs` exposes two constructors: `KonfMcpServer::new(...)` (defaults to `["*"]`) and `::with_capabilities(...)` (scoped).
- **`konf-mcp/src/main.rs:43` calls `KonfMcpServer::new(...)`.** The `with_capabilities` API exists but is not wired to the binary entry point — no CLI flag, no env var, no config lookup.
- `konf-backend/src/auth/middleware.rs` has JWT middleware with role claims, but that is HTTP-only, and MCP is not mounted in konf-backend today.

**Practical consequence:** whoever spawns `konf-mcp` stdio (ori via container subprocess, bert via ssh) gets full `*` access. The `roles.founder` / `roles.operator` / `roles.readonly` blocks in `operator/project.yaml` are documented but not runtime-enforced at the MCP layer.

For v0 this is acceptable: single-operator kell, both callers trusted, `founder ≥ operator` is trivially satisfied because both are `*`. The lattice becomes load-bearing when a second operator, adversarial scenario, or security review appears.

#### Proposed action when triggered
Two layers:

- **Layer A — wire the existing API to an env var (~5 lines of Rust in `konf-mcp/src/main.rs`).** Read `MCP_CAPABILITIES` as a JSON array; if set, call `with_capabilities(...)` instead of `new()`. No new features, just exposing an existing API. The container entrypoint exports `MCP_CAPABILITIES='["ai:*","shell:exec","http:*",...]'` for the operator role. bert's ssh path omits the env var and keeps `*`.
- **Layer B — an auth kell (see T-0038).** A reusable packaged kell with a workflow that reads `project.yaml`, takes a role name, and returns the capability list as JSON. The container entrypoint runs the workflow at boot, captures the output, exports as `MCP_CAPABILITIES`, then spawns konf-mcp. Role definitions stay in data; the wiring stays in a workflow; the binary only reads an env var. Layer B depends on Layer A.

**Bert's preference at the time of filing (2026-04-10):** defer both layers, connect first, add enforcement when forced. Accept that the `project.yaml` lattice is documentary for v0. Reopen when the trigger fires.

---

### T-0038 — Package-kells repo: reusable opinions as forkable bundles
- **opened:** 2026-04-10
- **opened-by:** bert
- **status:** future
- **owner:** bert (strategic) + broker (drafts when triggered)
- **priority:** low (cannot be justified until the second consumer exists)
- **trigger:** kell #2 is under construction and the first pattern from genesis is harvest-ready

#### Context
bert, verbatim (2026-04-10): *"if we build fundamental kells inside genesis as we go, which dont require rust changes, we can add them to a separate repository for other projects to use. some sort of konf package management."*

The idea: genesis is the first kell. As it is built, patterns will emerge that are reusable across kells — auth enforcement, common tool bundles, dashboard templates, CI pipelines, friction-log formats, doc-index patterns. Rather than bake these into genesis (single-use) or push them into konf's kernel (violates kernel minimalism — these are opinions, not primitives), harvest them into a separate repo of "packaged kells" that any kell can import.

Concrete candidates for harvest (priority order when the time comes):

1. **`auth.kell`** — role-to-capability workflow, uses the `MCP_CAPABILITIES` env var hook from T-0037 Layer A.
2. **`dashboard.kell`** — the terminal-boot-style dashboard template with `state.json` + boot animation + publish workflow.
3. **`tooling-basic.kell`** — common `tools.yaml` fragments (GitHub MCP sidecar, HTTP, embed, etc.) with sensible defaults.
4. **`friction-log.kell`** — the friction log format + append workflow + frontmatter schema.
5. **`docs-on-demand.kell`** — the OS/RAM split principle + docs-index.md pattern + the `just doctor` check for index freshness.

This is NOT genesis' concern yet. Building a package registry before there is a second consumer is YAGNI. Mentioned here so the harvest pattern is remembered when the moment comes — and so new patterns in genesis can be flagged with a "harvest candidate" comment when they look generalizable.

#### Proposed action when triggered

1. Wait for kell #2 to be under construction.
2. Identify ONE pattern from genesis that kell #2 would copy verbatim.
3. Create a `konf-packages/` sibling repo (or similar name — bert picks).
4. Extract the pattern as a packaged kell.
5. Import into both genesis and kell #2 via whatever mechanism emerges (git submodule? vendor copy? fetch workflow? to be designed).
6. Document the package format: what goes in, how to import, how to update.
7. Revisit after 3-5 harvested packages to formalize the package-management model.

---

### T-0039 — Privacy zones on the volume: kell vs private, filesystem-as-permission-boundary
- **opened:** 2026-04-10
- **opened-by:** bert (design addition during Phase 1.2 compose.yml drafting)
- **status:** landed in `docs/filesystem.md` (paper only); runtime enforcement happens at Phase 1.4 bootstrap
- **owner:** broker (documents + wires) + bert (approves the zone conventions)
- **priority:** medium
- **trigger:** Phase 1.4 VPS bootstrap — when the volume is actually partitioned and mount points are created

#### Context
bert, 2026-04-10: *"in the mounted volume of the vm we can have private stuff and data which is not visible to world and model directly without permissions, we have to think where things like hosted dbs, important configs, scratch pad dir with repos and experiments, archived stuff etc. all cloned repos, system logs etc go and what's safest and best. the vm can be deleted but only the volume can be transferred to other vms if we want to duplicate or scale etc."*

The idea: the attached volume holds more than just kell state. It holds bert's private material too — notes, personal configs, experiments in progress, cloned third-party repos, archives. The Broker should not see these. The volume is the unit of portability (VM is cattle, volume is data), and it must hold both kinds of material without the Broker being able to reach across the boundary.

**Solution documented in `docs/filesystem.md`:** split the volume into two top-level zones — `/srv/genesis/kell/` (bind-mountable into the opencode container; ori sees everything here) and `/srv/genesis/private/` (never mounted into any container; ori cannot reach it). The enforcement is by *construction* — the files are not mounted, so the Broker's process cannot observe them. No ACL, no permission check, no trust in self-restraint.

The boundary is also operationally cheap: to grant access, bert moves or copies a file from `private/` to `kell/workspaces/<project>/`. The existing bind mount picks it up without container reconfiguration.

#### What is landed in Phase 1.2 (paper only)
- `docs/filesystem.md` has a new "Two zones on the volume" section, a "privacy boundary is load-bearing" rationale, and an updated tree diagram showing both zones.
- `compose.yml` bind mounts respect the new layout: `${GENESIS_SECRETS_DIR:-/srv/genesis/kell/secrets}` and `${GENESIS_DATA_DIR:-/srv/genesis/kell/data}` default to the `kell/` subtree.

#### What is deferred to Phase 1.4 bootstrap
- `bootstrap/vps.sh` creates the `/srv/genesis/kell/{workspace,workspaces,data,secrets,backups,logs}/` and `/srv/genesis/private/{notes,configs,secrets,repos,experiments,archives}/` directory structure idempotently.
- Ownership: `/srv/genesis/kell/` owned by `konf:konf` mode `0750`. `/srv/genesis/private/` owned by `bert:bert` (or whatever the admin user is named) mode `0700`.
- Firewall rules and mount-time sanity checks live in the same bootstrap script.

#### Proposed action when triggered
Broker drafts the relevant bootstrap script sections in Phase 1.4. bert reviews the directory ownership before first run on the live VPS.

---

### T-0040 — Tailscale ACL: isolate kell from tailnet peers
- **opened:** 2026-04-10
- **opened-by:** broker (during VM bootstrap, F-002)
- **owner:** bert (Escalate — tailnet-wide governance)
- **priority:** high
- **trigger:** after Phase 1.4 smoke test passes and before the kell starts holding anything sensitive beyond test data

#### Context
During VM bootstrap the host firewall (ufw) was configured to permit inbound on `tailscale0` — which correctly scopes public access but also grants *every* peer on bert's tailnet the ability to reach 4096, 5432, 7420 on konf-genesis-0. More importantly, the kell itself can *reach* every other tailnet peer: bert's laptop, Macs, other VPSes, home devices. That directly contradicts the kell's `docs/privacy.md` invariant and the `docs/filesystem.md` "two zones" reasoning — absence-as-enforcement on the filesystem means little if the kell can reach arbitrary hosts over the network.

The ufw interface-level rule cannot fix this because ufw sees a single `tailscale0` device, not individual tailnet identities. The correct enforcement layer is Tailscale ACLs at the admin console.

#### Proposed action when triggered
Apply a Tailscale ACL roughly of the form:

```jsonc
{
  "tagOwners": {
    "tag:kell": ["autogroup:admin"],
    "tag:nginx-proxy": ["autogroup:admin"]
  },
  "acls": [
    // bert's personal devices reach kells freely
    { "action": "accept", "src": ["autogroup:self"], "dst": ["tag:kell:*"] },
    // nginx VPS reaches kell only on 4096 (reverse proxy to opencode)
    { "action": "accept", "src": ["tag:nginx-proxy"], "dst": ["tag:kell:4096"] },
    // kell can reply to bert and nginx but CANNOT initiate to other tailnet peers
    { "action": "accept", "src": ["tag:kell"], "dst": ["autogroup:self:*", "tag:nginx-proxy:*"] }
    // implicit default-deny: kell → any other tailnet peer is denied
  ]
}
```

Steps:
1. Tag `konf-genesis-0` with `tag:kell` in the admin console
2. Tag the nginx VPS with `tag:nginx-proxy`
3. Back up the current ACL JSON (so rollback is one paste)
4. Apply the new ACL
5. Verify from bert's laptop: ssh genesis, reach :4096 → still works
6. Verify from the kell: attempt to reach another tailnet peer → refused
7. Verify from another tailnet peer (not tag:kell): attempt to reach konf-genesis-0 → refused

#### Why this wasn't done in the same session as the firewall
Overwriting a Tailscale ACL without seeing the current state risks locking the entire tailnet out. Escalate-class governance action, broker should not touch it. Deserves its own focused pass with rollback-ready state.

#### Related
- `docs/privacy.md` — the "no public ports" invariant this completes at the tailnet layer
- `docs/filesystem.md` § "Two zones" — absence-as-enforcement reasoning
- T-0035 — Tailscale deployment mode (host-level, already resolved — tailscale was pre-installed on the VM)
- T-0031 — nginx proxy + Cloudflare (consumer of `tag:nginx-proxy`)

---

## Rules for this file

1. **Every deferral gets a thread.** If you say "later", you write a thread. No exceptions.
2. **Every thread has a trigger.** "Later" without a condition is a silent drop.
3. **Every thread has an owner.** Orphaned threads rot.
4. **Closed threads move to `threads-closed.md`** with an outcome note. This file only carries open work.
5. **New thread IDs are monotonic.** T-0020, T-0021, ... never reused.
6. **Bert can open, edit, or close any thread** with a commit. The Broker surfaces and proposes but bert's word is final on bert-owned threads.
