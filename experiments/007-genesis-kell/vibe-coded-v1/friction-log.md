---
title: Friction log
kell: genesis
audience: [user-facing, ai-facing]
publish: true
---

# Friction log

> *The narrative of what broke, what was surprising, and what was learned. Public-safe by default; entries with `private: true` in their header are filtered from the published dashboard but stay in git history — we do not hide things, we just curate what is surfaced.*

Every surprise, every deferral, every kernel gap, every doctrine drift becomes an entry here. The log is the honest record of the experiment. Readers — humans and future versions of ori — will use it to judge whether the doctrine actually holds under pressure.

Write honestly. Write what broke. Write what you learned. If you were wrong, say so. Polish does not compound; honesty does.

Entry format:

- `F-NNN` monotonic ID, `YYYY-MM-DD`, short descriptive title in the heading.
- **What happened** — the event, the surprise, the drift. Concrete and specific.
- **How it was caught** — the accident or process that surfaced it. Sometimes the caught-how is more interesting than the break itself.
- **What I changed** — files, commits, threads opened or closed.
- **What I learned** — the rule going forward. The reason the entry exists.
- **Related threads / commits** — cross-refs.

---

## F-001 — 2026-04-10 — Phase 1.1 misread konf's MCP transport as HTTP/SSE

**What happened.** During Phase 1.1 (the OpenCode schema verification pass), I — ori, pre-kell, running as Claude Code on bert's laptop — read the source comment in `konf/crates/konf-mcp/src/main.rs:6`:

> "SSE transport is available when mounted in konf-backend at /mcp."

and resolved the MCP transport known-unknown (`docs/runtime-opencode.md` KU-5) with:

> "OpenCode's `type: "remote"` client tries StreamableHTTP first, falls back to SSE. Konf's SSE endpoint at `/mcp` is compatible."

This was wrong. I read the comment as describing current reality; it was aspirational. The very next file I should have checked — `konf/crates/konf-backend/src/main.rs:140-141` — says explicitly:

> "MCP is served via konf-mcp binary (stdio transport), not embedded in HTTP server. SSE transport for MCP will be added when rmcp's StreamableHttpService is integrated."

The HTTP/SSE MCP endpoint does not exist today. `konf-mcp` is stdio-only. My entire Phase 0.5 wiring of `operator/opencode/opencode.jsonc` — `type: "remote"` with `http://konf:8765/mcp` — would have failed at first boot with a connection error because nothing is listening there.

**How it was caught.** Phase 1.2 started with a sanity grep of `konf-backend/src/main.rs` for port binding, intending to confirm the port number before writing `compose.yml`. The grep surfaced the comment at line 140-141, which triggered a wider search, which revealed the drift. I had not done this grep during Phase 1.1 because I trusted the header comment in `konf-mcp/src/main.rs:6` without tracing the call graph.

**What I changed.**

- Reopened `runtime-opencode.md` KU-5 and rewrote it with the real answer (stdio today, HTTP planned but not shipped).
- Rewrote `operator/opencode/opencode.jsonc` `mcp.konf` block: `type: "local"` with `command: ["konf-mcp", "--config", "/kell/operator"]`. Deleted `url`, `headers`, `oauth` fields.
- Rewrote `docs/filesystem.md` compose topology: removed the separate `konf` service; konf-mcp is now a binary bind-mounted into the opencode container and spawned as a stdio subprocess.
- Rewrote `docs/spec.md` success criterion 2 from "Claude Code calls konf MCP endpoint directly" to the dual-path model: bert reaches opencode over Tailscale for Broker-mediated work and reaches konf-mcp directly via ssh-tunneled stdio for direct access.
- Cleaned stale env vars from `env.example/shared.env` (`MCP_PORT`, `MCP_BIND`) that assumed an HTTP port to bind.
- Wrote `compose.yml` with two services (opencode, postgres), the konf-mcp binary bind-mounted from `../konf/target/release/konf-mcp`.
- Filed T-0037 (stdio MCP has no auth enforcement) and T-0038 (package-kells repo as future home for reusable opinions).

**What I learned.**

1. **Aspirational doc comments are dangerous.** Lines like "X is available when Y" read as current reality to someone scanning the codebase fast. The fix is not only on my side (read more carefully); future comments of this kind in the konf codebase should be marked explicitly as "WILL BE," "planned," or dated. Filed a mental note for a konf-side cleanup when the MCP-HTTP integration lands.

2. **Verify bottom-up, not top-down.** I trusted a comment in a header without tracing the call graph to confirm. A single grep for `StreamableHttpService` or `mount.*mcp` in `konf-backend` would have caught this in Phase 1.1. Rule going forward: verification passes start from the entrypoint and trace forward to the implementation, not from the header and trust the narrative.

3. **Research-to-facts has its own risk surface.** Phase 1.1 was supposed to turn hypotheses into facts. I turned one hypothesis into a *different* hypothesis and marked it "resolved." The fix is not to do less research; it is to raise the bar for what counts as "verified." A verified fact should cite a call site, not a comment.

4. **Misreads should feel expensive.** The cost of this misread was a few hours of doc churn and one friction log entry. Cheap because Phase 1 is still paper-only. At Phase 2 (live on the VPS), this misread would have been a late-night boot failure, maybe a rollback. The entry is the forcing function to make me slower next verification pass.

5. **The friction log is doing its job on entry one.** Writing this entry took real effort and surfaced details I would not have captured in a commit message alone. The "would I publish this?" bar on the entry makes me honest about the mistake in a way a private todo list would not.

**Related threads:**

- **T-0037** — stdio MCP has no auth enforcement. The misread surfaced this adjacent gap: even if HTTP had existed, the stdio path has zero runtime enforcement of `project.yaml` roles. Deferred for v0; see the thread for the full argument.
- **T-0038** — package-kells repo as the future home for reusable opinions like auth. Bert's idea during the reconciliation conversation; worth capturing.

**Related commits:**

- `6bcf07e` (Phase 1.1) — contained the misread. The KU-5 section of `runtime-opencode.md` in this commit is wrong; the Phase 1.2 commit reverts and corrects it.
- Phase 1.2 commit (pending) — contains the reconciliation bundle.

---

## F-002 — 2026-04-10 — First VM boot: everything is drift

**What happened.** Attempted to boot the kell on the Hetzner VM (`ssh genesis`, `konf-genesis-0`, Ubuntu 24.04, 2 vCPU / 3.7 GiB RAM / 50 GB volume). Made real progress on infrastructure but hit a cascade of drift between the paper design and reality. Session ended in plan-mode pause because the drift is large enough that continuing to patch is the wrong move — the next session needs a fresh plan from evidence, not more improvisation. This entry is the evidence dump.

### What actually worked

- **Two-zone remount.** `/dev/sdb` moved from `/mnt/cell-data` to `/srv/genesis` via UUID-based fstab. Clean umount, clean remount, verified.
- **Three-zone tree created.** `/srv/genesis/{kell,private,infrastructure}/*` with correct ownership: `konf:konf` on kell, `root:root` on private (0700), `konf:konf` on infrastructure. See "Drift 5" below for the three-zone addition.
- **`konf` service user.** Created with lingering, bert's admin key in `authorized_keys`, owns kell and infrastructure zones.
- **System packages.** podman 4.9.3, podman-compose 1.0.6, just 1.49.0, rust 1.94.1, git, jq, ufw.
- **Swap.** 4 GiB swapfile added preemptively; konf-mcp release build consumed peak 768 K of swap (insurance, not required on cx22).
- **konf-mcp built.** 16m30s release build, 53 MB binary. Loads product config from `operator/` correctly; registers 14 tools + 1 resource when `--config` is pointed at the kell's operator dir.
- **Postgres as VM-level service.** `genesis-postgres` container running under rootless podman, bound to `localhost:5432`, health check green, `konf` db and role exist. Moved to the `infrastructure/postgres/` zone.
- **ufw active.** `default deny incoming`, `allow 22/tcp`, `allow in on tailscale0`, outgoing open. Survived the enable (SSH still worked).
- **Tailscale ACL isolation.** After two iterations (first attempt missed that `tag:ssh` was a secondary tag on the kell), the kell now cannot initiate to any other tailnet peer. Verified: `baadal:22` ssh attempt drops silently, ping drops 100%, `llm.bertslab.com` still reachable. `tag:ai-operator` is the only tag on `konf-genesis-0`. ACL JSON is in bert's Tailscale admin console with tests that re-run on save.
- **OpenCode installed as host process.** `~konf/.opencode/bin/opencode` (168 MB), version 1.4.3. Runs under a systemd `--user` unit with lingering. SQLite migration complete. `opencode server listening on http://0.0.0.0:4096`.
- **Gate 1 ✓** (stack up).
- **Gate 2 ✓** (Broker persona loaded): `/agent` returns `broker` as the first entry with mode `primary`, proving `OPENCODE_CONFIG` + `opencode.jsonc` + file-ref to `broker.md` all resolve.
- **MCP connection up:** `curl http://localhost:4096/mcp → {"konf":{"status":"connected"}}`.

### Drift 1 — `compose.yml` defaults bypassed the two-zone privacy boundary

Found pre-VM. `compose.yml` defaulted `GENESIS_SECRETS_DIR=/srv/genesis/secrets` and `GENESIS_DATA_DIR=/srv/genesis/data`, but `justfile` defaulted to `/srv/genesis/kell/secrets` and `/srv/genesis/kell/data`. Silent drift: `just doctor` would check one set of paths and `podman-compose up` would bind another. On the VM this would silently create `/srv/genesis/secrets/` outside the kell zone, violating the load-bearing privacy invariant.

Also found stale `/srv/genesis/...` paths (no `kell/` prefix) in `docs/filesystem.md`, `docs/runtime-opencode.md`, `docs/threads.md`, `operator/opencode/opencode.jsonc`. Fixed all in PR #3 (merged as `eba77ba`).

**Rule:** when a load-bearing invariant crosses multiple files, grep for every path variant before declaring doc drift "fixed." One grep across the kell root would have caught all 10+ stale references in one pass.

### Drift 2 — four governance rules all needed fixing to land a PR

Single-maintainer repo + GitHub branch protection rules that assume multi-person review blocked every attempt to merge the fix. In order of discovery:

1. `require_last_push_approval: true` — impossible to satisfy solo. Flipped to false. Was the visible symptom the previous handoff had already hit.
2. `required_approving_review_count: 1` — GitHub blocks self-approval at the API layer, so even admins cannot satisfy it solo. Flipped to 0. This was my misread: commit `923b493` claimed rule 1 was THE blocker; wrong.
3. `required_signatures: true` — commits made by the broker via regular git were unsigned. I hadn't realized bert had no signing key set up. Pivoted to SSH commit signing with bert's `id_duncan` SSH key; SSH signing key registered on GitHub; git config `gpg.format ssh`; rebase `--exec "git commit --amend --no-edit -S"` over `HEAD~3` to sign all branch commits.
4. `required_status_checks.contexts: ["dashboard / security"]` — GitHub Actions publishes the check as just `"security"`, not `"dashboard / security"`. The UI display format is different from the API context name. Fixed to `["security"]`. This was a pre-existing bug in the JSON; PR #2 only merged because it happened during a temporary `enforce_admins: false` window which bypassed this broken rule along with the others.

**Rule:** every rule in `branch-protection.json` needs to be tested by actually trying to merge a PR through the rule. Paper review doesn't catch impossible-to-satisfy rules. The checkmark isn't that the rule exists; it's that a real PR got through with the rule enforced.

**Rule for 008-org-chart:** each of these was an `Escalate`-class action (governance layer). The broker correctly handed each one to bert rather than executing. The round-trip cost (broker proposes → bert executes via `gh api PUT`) was real — four full cycles — and the friction is proportional to how many governance bugs existed. With cleaner governance, Escalates should be rare.

### Drift 3 — the broker leaked LITELLM_API_KEY in a diagnostic dump

While debugging opencode config loading, the broker ran `cat /proc/$PID/environ | tr '\0' '\n' | grep -iE '...'` and the grep's `-i` mode + the variable name `LITELLM` causing a case-insensitive match + a permissive pattern meant the full key value appeared in tool output. The key value is now in the broker's conversation context, bert's terminal scrollback, and any session persistence the harness does. Bert rotated the key immediately; session resumed with a fresh key.

**Rule:** environment dumps must be **allowlist-based**, not blocklist-based. List the variables you want to see, never grep and hope the exclusion holds. A single typo in a blocklist is a leak. Concretely: use `grep -E '^(OPENCODE_CONFIG|KELL_ROOT|KONF_MCP_BINARY)='` form, never `grep -vE 'SECRET|KEY|TOKEN'`.

**Rule for the konf-tool-secret layer (T-0044 candidate):** workflows that need diagnostic env snapshots should call a `secret:mask` helper that redacts any key registered in the `allowed_keys` list. Dispatching the dump through an audited tool rather than raw shell means the mask is centralized.

### Drift 4 — `compose.yml` bound to `ghcr.io/sst/opencode:latest` which does not exist

First `just up` pulled postgres fine, then 403 Forbidden on `ghcr.io/sst/opencode:latest`. All tags tried (latest, main, stable, v0.1.0) returned 401/403. **OpenCode is distributed as a binary / npm package, not a container image.** The `compose.yml` default was a paper guess that was never verified against reality. bert's own laptop has `~/.opencode/bin/` — he installed via the bash installer, never as a container.

Pivoted to: install opencode natively on the VM as the `konf` user via the bash installer, run it under systemd `--user`, skip containerization for the opencode layer entirely. Container boundary (defense-in-depth layer 2) is lost for opencode; the Unix user boundary, the ufw rules, and the tailscale ACL carry that load. Postgres stays containerized as a VM-level service.

**Rule:** every image tag in a compose.yml must be verified to exist before the file is committed. Don't carry paper guesses through to F-002 and find them at boot.

### Drift 5 — the "two-zone" filesystem model is actually three-zone

bert asked: *"can we have one common postgres deployed in the mounted dir, one postgres service for all purposes (with namespace) as smrti also uses it?"*

Correct question. The `konf/products/genesis/kells/operator/config/services.yaml` file in the konf repo already declares infrastructure at the **VM level** (postgres, infisical, ollama), not at the per-kell level. The `konf-genesis.kell` repo was designing postgres as kell-scoped in `compose.yml` — which was a mental-model drift. On a single VM with multiple kells (konf-genesis, smrti, future assistant, future devkit), there should be **one postgres**, **one database per consumer**, namespaced by role + database name.

Added `/srv/genesis/infrastructure/` as a new top-level zone alongside `kell/` and `private/`. Filesystem.md needs a full pass. Filed as T-0041 (pending — this entry is the source record).

The deeper drift: **`konf-genesis.kell` is not using the konf product model it should be.** `konf/products/genesis/kells/operator/` has `project.yaml`, `tools.yaml`, `services.yaml`, `workflows/*.yaml` — declarative product definitions that the `init` kell provisions. The `konf-genesis.kell` repo has a `compose.yml` + `justfile` — imperative runtime artifacts. The right long-term model is to reconcile the two: the kell declares services and tools via YAML; the init kell provisions them. Filed as T-0042.

### Drift 6 — `env/shared.env` still has container-era path defaults

`KELL_ROOT=/kell` and `OPENCODE_CONFIG=/kell/operator/opencode/opencode.jsonc` in `env.example/shared.env`. These are container-relative paths that made sense when opencode ran inside a container with `/kell` bind-mount. Running opencode natively on the host, they're wrong (host path is `/srv/genesis/kell/workspace/konf-genesis.kell`).

On the VM I patched `env/shared.env` directly to host paths. The template (`env.example/shared.env`) still has the container-era values and hasn't been updated — that's deferred to T-0041/T-0042 scope. Systemd `Environment=` directives cannot override `EnvironmentFile=` values in all systemd versions; I reordered the unit and it still didn't override. File-patching was the only reliable path.

**Rule:** when running services natively (no container), env-file templates written for container-relative paths must be patched per host. Either the template gains a comment block showing both forms, or there's a bootstrap step that rewrites them.

### Drift 7 — `opencode.jsonc` has a doctor-parser hazard in its comments

`just doctor` greps `opencode.jsonc` for `{file:...}` patterns to verify file-refs resolve. The grep is regex-only and not JSONC-aware, so a commented-out example `{file:./operator/prompts/broker.md}` inside a `//` comment was picked up as a real file-ref, which failed to resolve, which failed doctor. Rewrote the comment to describe the pattern without using literal `{file:...}` syntax.

**Rule:** when parsing config files, use a proper parser (jsonc-to-json or native JSONC), never regex. Or: document that comments in opencode.jsonc must not contain literal `{file:...}` forms. The rule of choice is the parser upgrade; the quick fix was the comment rewrite.

### Drift 8 — `opencode serve` has no HTTP session/message API

Spent real time probing `/session`, `/sessions`, `/project`, `/message`, etc. The `/doc` OpenAPI spec enumerates exactly 9 endpoints, all `/global/*` or `/auth/*` or `/log`. There is **no HTTP API for sending prompts**. `opencode serve` is designed to be an MCP host and a UI surface for clients that connect over MCP or via the TUI. Headless prompts use `opencode run` (a separate CLI invocation that shares the same config).

**Rule:** when the runbook says "hit the API with a prompt," verify the API exists first. `opencode run` is the headless path, not `curl`.

### Drift 9 — Gate 3 is undecidable because local models don't emit tool calls via the API structure

Fired `opencode run --agent broker --model bertslab/local/fast "Call the konf echo tool..."`. The broker activated, reached the provider, and emitted **`{"name": "konf_echo", "arguments": {"message": "ping"}}` as assistant text content** — not as a tool call through the API structure. Same result with `bertslab/local/coder`. `bertslab/openrouter/anthropic/claude-opus-4.5` returned a 402 (OpenRouter credits exhausted).

Two possible causes:
1. The local models on bert's LiteLLM proxy are small / quantized / not fine-tuned for OpenAI-format tool calling. They "know" what a tool call looks like because it's in their training data, so they hallucinate JSON text in the assistant message content. But they don't use the `tool_calls` field of the chat completion API.
2. The tools weren't advertised to the model at all. `/mcp` says `konf: connected` but that doesn't prove the tool list made it into the chat completion request body. opencode may be calling the LLM without a `tools` parameter for some models.

**Evidence for hypothesis 1:** the models produced the call even when no tools were exposed — hallucinated behavior consistent with "I think I should pretend to call echo here."

**Evidence for hypothesis 2:** didn't verify by capturing the actual outgoing request from opencode to llm.bertslab.com. Required to distinguish: tcpdump or tee the LiteLLM proxy logs on bert's proxy host.

**Rule for next session:** Gate 3 needs a two-layer verification. First, prove the tool is actually advertised to the model (capture the outgoing chat/completions request body, look for `"tools"` array). Second, prove the model can call tools (use a model known to support tool calling; openrouter top-ups or top up bert's proxy with claude-sonnet access). Both layers matter; passing the first without the second is still a fail.

### Drift 10 — the kell is bootable but not yet useful; smoke-test.md's four gates need reframing

Original smoke-test.md runbook had:
- Gate 1: `just up` completes
- Gate 2: OpenCode loads `broker.md`
- Gate 3: Broker calls a konf tool
- Gate 4: konf reaches postgres

Reality: Gates 1 and 2 are pass/fail on infrastructure (both passed). Gates 3 and 4 are pass/fail on model capability + MCP wiring + DB wiring, each of which is its own test layer. Bundling them into one "gate" hid the actual failure mode: we don't know if the bug is the model, the tool advertisement, the MCP tool list, or the DB config. Each wants its own probe.

Bert's response: *"make sure every component works one at a time step by step and move up. make a plan to do that, we can automate this as a health check workflow if you think you can use it a bunch of times. insight -> for us saving info as yaml workflow is same or even easier to do than as readmes."*

This is the right frame and it's the input to the next session. The health check is a konf workflow (YAML), composed of leaf probes at L0 (infrastructure), L1 (binaries), L2 (config loading), L3 (LLM connectivity), L4 (end-to-end). Each probe asserts one thing. The whole workflow runs on demand or on a schedule. The workflow YAML IS the documentation of the health contract — no separate doc.

### Process observations for 008-org-chart

- **Every Escalate cost a full round-trip.** The friction was real. The four-rule governance bug + the key rotation + the ACL two-pass + the tag removal all required bert's hand. The broker never touched an identity/governance surface. That boundary held.
- **Mental-model drift is the dominant failure mode, not execution errors.** 9 of 10 drifts in this entry are "the paper said X but X is wrong / incomplete / misaligned with reality." The execution of each corrective action was straightforward; the cost was in discovery. A good retrospective format should capture drifts separately from errors.
- **Saving state to YAML workflow is the same or cheaper than README prose.** Bert's insight. If true, it reframes what "documentation" means for this kell: executable health checks and runbooks, not narrative walkthroughs. The narrative survives as friction logs; the operational knowledge survives as workflows.
- **Session ended by mutual decision.** Plan mode pause was triggered by realization that the scope had outgrown the initial plan. Stopping and dumping raw evidence is a better exit than forcing closure.

### What is landed in this commit

- `docs/threads.md` — T-0040 filed (Tailscale ACL isolation; since resolved and live on the tailnet, but recording the work).
- `operator/opencode/opencode.jsonc` — LiteLLM provider block replacing Anthropic, `bertslab/local/fast` as default broker model, env-var substitution for the MCP command path, comment rewrite to work around the doctor parser hazard.
- This friction log entry (F-002).

### What stays uncommitted on the VM (intentional, for next session)

- `/srv/genesis/infrastructure/postgres/` live and healthy.
- `~konf/.config/systemd/user/opencode.service` — native systemd unit, runs opencode.
- `~konf/.opencode/bin/opencode` — the 1.4.3 binary.
- `/srv/genesis/kell/workspace/konf-genesis.kell/env/*.env` — populated with real values, host-native path overrides.
- ufw rules, Tailscale ACL (latter lives in bert's admin console, not the kell repo).

The next session should run the health check workflow as its first action to confirm the VM state matches this log.

### Open threads opened or touched in this session

- **T-0040** — Tailscale ACL isolation (resolved operationally; keeping the thread open until the filesystem.md + privacy.md cross-refs are updated to reflect the tailnet layer).
- **T-0041** (pending filing) — three-zone filesystem: add `/srv/genesis/infrastructure/`, update `docs/filesystem.md` + `docs/privacy.md` + fork note.
- **T-0042** (pending filing) — reconcile `konf-genesis.kell` runtime model with `konf/products/genesis/kells/operator/` declarative product model.
- **T-0043** (pending filing) — set `OPENCODE_SERVER_PASSWORD` on the systemd unit so the HTTP API isn't unsecured on the tailnet.
- **T-0044** (pending filing) — centralised secret masking helper for diagnostic env dumps (prompt by the LITELLM_API_KEY leak).
- **T-0045** (pending filing) — health check workflow as YAML, layered L0–L4, usable as the canonical "does the kell work" probe.
- **T-0037** — stdio MCP auth still untouched. Unchanged from previous sessions.

### Related commits

- `eba77ba` — the two-zone path unification + branch protection fix (PR #3, merged during this session).
- (this commit) — F-002 + the pending paper-level changes from the VM work.
