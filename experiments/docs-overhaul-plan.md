# Plan: Documentation Overhaul — Phases

## Context

Post-merge to main. Kernel uses colons (`memory:search`), docs mostly say underscores. Multiple sessions have made changes. The docs are layer 0 configuration — they must be perfect, coherent, and honest. No exaggeration, no stale claims, no contradictions.

## Key Decisions

1. **Archive + research docs:** Tag current commit as `v0.1.0`, then delete archive/ and research/ from working tree. Historical docs live in git history at the tagged version.

2. **Findings:** Create a single `FINDINGS-RESOLUTION.md` in konf-experiments that summarizes all findings with their final resolution. Keep individual finding files but mark them as session artifacts (not to be loaded into LLM context unless debugging). The system for this: findings get a `status:` frontmatter field (open/fixed/superseded) and a `resolution:` section.

3. **Terminology:** Konf is the project/platform. A "kell" is what Konf creates — a named computational boundary of configuration (from Kell calculus). Architecture docs adopt this: "product" → "kell" where it refers to the configuration bundle. "Product" can still be used informally.

4. **Tool naming in docs:** All docs use colons (`memory:search`, `ai:complete`). The MCP adapter is documented as a transparent translation layer.

## Phase 1: Foundation (tag + delete stale docs)

**Goal:** Clean slate. Remove docs that pollute context. Tag the old state.

**Steps:**
1. Tag current commit as `v0.1.0` in konf repo
2. Delete `docs/archive/` (5 files) — phase plans consumed into master-plan
3. Delete `docs/research/` (6 files) — consumed into architecture docs
4. Update any cross-references that pointed to deleted files
5. Commit: `chore: tag v0.1.0, remove stale archive and research docs`

**Files deleted (~11):**
- `docs/archive/2026-04-05-konflux-rewrite.md`
- `docs/archive/2026-04-06-master-implementation-plan.md`
- `docs/archive/2026-04-06-phase-a-hardening.md`
- `docs/archive/2026-04-06-phase-b-konf-runtime.md`
- `docs/archive/2026-04-06-phase-c-konf-backend.md`
- `docs/research/2026-04-05-runtime-architecture-survey.md`
- `docs/research/2026-04-05-runtime-recommendation.md`
- `docs/research/2026-04-06-architectural-claims-evaluation.md`
- `docs/research/2026-04-06-real-world-experiences.md`
- `docs/research/2026-04-06-rust-ecosystem-survey.md`
- `docs/research/2026-04-06-universal-storage-and-edge-vision.md`

**Verification:** `grep -r "docs/archive\|docs/research" docs/` — no dead links remain.

---

## Phase 2: Tool naming sweep (colons everywhere)

**Goal:** Every doc reflects the kernel truth: colon-separated tool names.

**Steps:**
1. Re-read each architecture doc and verify/update tool name references
2. Re-read each product guide doc and update
3. Re-read getting-started docs and update
4. Re-read admin guide docs and update
5. Update example workflow YAMLs in docs
6. Update `docs/plans/unspool-migration.md` — either update tool names or mark clearly as "pre-kell-naming draft"
7. Update README.md examples if they reference tool names

**Key replacements:**
- `memory_search` → `memory:search`
- `memory_store` → `memory:store`
- `ai_complete` → `ai:complete`
- `ai_embed` / `embed_text` → `ai:embed`
- `http_get` → `http:get`
- `http_post` → `http:post`
- `shell_exec` → `shell:exec`
- `config_reload` → `config:reload`
- `system_introspect` → `system:introspect`
- `yaml_validate_workflow` → `yaml:validate_workflow`
- `state_set/get/delete/list/clear` → `state:set/get/delete/list/clear`
- `workflow_*` → `workflow:*` (capability patterns)
- `memory_*` → `memory:*` (capability patterns)
- `state_*` → `state:*`

**Open question (from Gemini audit):** `schedule` has no namespace prefix but `cancel:schedule` does. Options:
- Rename to `schedule:run` + `schedule:cancel` (consistent)
- Keep `schedule` + `cancel:schedule` (schedule is a top-level primitive, cancel is its operation)
- Decision: resolve at execution time, not in this plan

**Critical:** Do NOT blindly sed. Read each doc, understand context, update correctly. Some mentions are in code blocks showing MCP client output (which SHOULD show underscores since that's what the adapter produces).

**New section needed:** Document the MCP name adapter in `docs/architecture/mcp.md` — explain that kernel uses colons, MCP clients see underscores, translation is transparent.

**Verification:** 
- `grep -rn "_search\|_store\|_complete\|_exec\|_get\|_post" docs/ --include="*.md"` — only in MCP/client context
- All YAML examples in docs use colons

---

## Phase 3: Adopt kell terminology

**Goal:** Introduce "kell" consistently where appropriate.

**Steps:**
1. Add a "Terminology" section to `docs/architecture/overview.md` defining kell
2. Update `docs/architecture/design-principles.md` Principle 9 — "Kells are configuration, not code" (was "Products are configuration, not code")
3. Update `docs/product-guide/creating-a-product.md` → "Creating a Kell" (or "Creating a Product (Kell)")
4. Update `docs/product-guide/configuration.md` — use kell where referring to the config bundle
5. Update README.md — introduce kell in the "What is Konf?" section
6. Leave architecture internals (engine, runtime, init) using "product" in code-facing docs — the Rust code still uses `ProductConfig`, and we don't rename code in a docs phase
7. Add etymology note: "From the Kell calculus (Schmitt & Stefani, 2003-2007) — a named computational boundary"

**Principle:** Kell is the user-facing term. ProductConfig is the Rust struct. Docs that describe YAML use "kell". Docs that describe Rust internals use "product config".

**Contributor bridge (from Gemini audit):** Add a terminology section to CONTRIBUTING.md:
- "kell" = what users create (YAML config bundle)
- "ProductConfig" = what Rust code handles (the struct)
- "product" = informal term, acceptable in conversation, maps to kell

**Verification:** Search for "product" — every usage should be either code-facing (ProductConfig) or accompanied by "(kell)" on first mention.

---

## Phase 4: Update status, roadmap, and findings

**Goal:** All status docs reflect the actual current state. Findings get resolution summaries.

**Steps:**

### 4a. Create `konf-experiments/FINDINGS-RESOLUTION.md`
Single document summarizing all findings and their final state:

| Finding | Title | Status | Resolution |
|---------|-------|--------|------------|
| 001 | Tool names with colons | RESOLVED | Colons restored. MCP adapter translates at boundary. |
| 002 | MCP annotations drop | FIXED | Annotations not sent to avoid Claude Code bug. |
| 003 | Hot-reload invisible | ALREADY WORKED | `notify_tool_list_changed()` was implemented. |
| 004 | MCP infra access | ADDRESSED | `scope_from_role()` + `with_capabilities()`. Dev mode keeps full access. |
| 005 | with: vs input: | DOCUMENTED | Only `with:` exists. Docs updated. |
| 006 | Template prefix | DOCUMENTED | `{{input.x}}` required. Docs updated. |
| 007 | Env interpolation | ALREADY WORKED | `interpolate_env_vars()` was implemented. Docs were wrong. |
| 008 | Single entry node | BY DESIGN | First YAML node = entry. Documented. |
| 009 | Nested workflows | VERIFIED | Was config issue, not code bug. |
| 010 | Docs gaps | FIXED | workflow-reference.md comprehensive rewrite. |
| 011 | catch: schema | FIXED | CatchBlock untagged enum — string or array. |
| 012 | MCP bypasses guards | BY DESIGN | Guards at runtime scope, not MCP layer. |
| 013 | No cancel_schedule | FIXED | cancel:schedule tool added. |
| 014 | Only one new Rust tool | VALIDATED | schedule tool is the only kernel addition for autonomy. |
| 015 | F2 not needed | VALIDATED | Push-based scheduling sufficient. |

### 4b. Add frontmatter to each finding file
```markdown
---
status: fixed|by-design|open
resolution: "One-line summary"
---
```

### 4c. Update `konf-experiments/experiments/ROADMAP-2026-04-09.md`
- F1, F4: mark DONE with what was actually built (GuardedTool, not hooks)
- Experiment 004: mark COMPLETE with results
- Colon naming: document the journey and final decision
- kell terminology: note adoption

### 4d. Update `konf-experiments/experiments/STATUS-2026-04-09.md`
- Ensure all phases reflect current state
- Finding 001 resolution updated (colons restored, adapter added)

**Verification:** Read ROADMAP → STATUS → FINDINGS-RESOLUTION → architecture docs — no contradictions.

---

## Phase 5: Missing documentation

**Goal:** Document features that exist but have no docs.

**Steps:**
1. **`schedule` + `cancel:schedule` tools** — add to `docs/product-guide/tools-reference.md` with return types, required capabilities, and `schedule_id` linkage between the two tools
2. **Tool guards** — add examples to product guide (currently only in runtime.md architecture doc)
3. **MCP name adapter** — new section in `docs/architecture/mcp.md`
4. **Roles config** — add to `docs/product-guide/configuration.md` (currently only in runtime.md)
5. **Design principles 11-13** — verify they're in the doc (they are, but verify cross-references)
6. **Nightwatch example** — add to product guide as "Example: Autonomous Agent"

**Verification:** Every feature in the codebase has a corresponding doc entry. Check by listing all registered tools and verifying each appears in tools-reference.md.

---

## Phase 6: Final coherence check

**Goal:** Read every remaining doc end-to-end. No contradictions, no stale claims, no exaggeration.

**Steps:**
1. Read README.md — does it honestly represent what exists?
2. Read each architecture doc — does it match the code?
3. Read each product guide doc — can a user follow it?
4. Read admin guide — can an operator deploy?
5. Read CLAUDE.md and GEMINI.md — do they match the architecture?
6. Verify all cross-links between docs work
7. Run `cargo test --workspace` to confirm code matches doc claims
8. Verify all `products/` example YAMLs use correct tool names (from Gemini audit)
9. Every tool in tools-reference.md includes: name, description, input schema, return type, required capability

**Verification:** A developer cloning the repo understands what Konf is, how it works, and can build a kell within 30 minutes.

---

## Execution Strategy

- Each phase is one session (or less)
- At the start of each phase, re-read the relevant files fresh (don't rely on prior context)
- After each phase, commit and push
- Phases can be done sequentially over multiple sessions
- Phase 6 is the final gate — no merge to "stable" tag until it passes

## Phase Order

```
Phase 1 (tag + clean) → Phase 2 (colon naming) → Phase 3 (kell) →
Phase 4 (status/findings) → Phase 5 (missing docs) → Phase 6 (coherence)
```

Each phase depends on the previous. Don't skip.

## Phase 7: Independent audit from first principles

**Goal:** A fresh agent with zero context from this conversation reads the entire docs surface and evaluates it from scratch. No inherited assumptions, no "I know what I meant."

**Steps:**
1. Spawn a new agent (not a continuation of any prior agent)
2. The agent reads ONLY the docs and code — no session history, no plan file, no memory
3. It answers these questions from first principles:
   - What is Konf? (Should be clear from README + overview alone)
   - How do I create a kell? (Should be clear from product guide alone)
   - How do tool names work? (Colons internally, underscores in MCP — no contradictions?)
   - Are the 13 design principles reflected in the actual architecture?
   - Do the examples compile and run as documented?
   - Are there any claims that aren't backed by code?
   - Are there any features in code that aren't documented?
   - Would a new contributor understand how to start?
4. The agent produces a findings report with: confirmed truths, contradictions found, gaps found, suggestions
5. Any findings are fixed before tagging the docs as stable

**Why a separate agent:** We wrote the docs. We can't objectively audit our own work — we'll fill in gaps with memory instead of noticing them. A fresh agent has no context to fill gaps with. It sees only what's on the page.

**Constraint:** The audit agent must NOT be told "this was recently updated" or "we just changed X." It reads the docs as a stranger would.
