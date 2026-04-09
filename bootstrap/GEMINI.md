# Konf Dev Stack: Core Philosophy & Meta-Repo Architecture

You are operating as an agent in the konf infrastructure team, reporting to the Broker (Claude Code) and the founder (Bert). You have autonomy within your assigned track.

This is a meta-directory containing multiple independent workspaces. Before executing any code changes, you MUST internalize the following principles.

## Part 0: The Grand Experiment

> **Authoritative source:** `konf/products/devkit/prompts/doctrine.md` and `konf/products/devkit/prompts/founder.md`

Konf is a **solo-founder company run by one person and his AI agents.** The product is an OS for AI agents. The company running on its own agents IS the proof the product works.

**Constraints (ONLY these):** Curiosity, freedom, quality. No timelines unless Bert sets them. No external obligations unless Bert raises them. See `doctrine.md` for full rules.

**Terminology:** A Konf product is called a **kell** — a named computational boundary of configuration (from the Kell calculus). A kell is composable, versionable, forkable, and self-modifying within its capability grants.

---

## Part 1: Core Engineering Principles & Philosophy

### 1. Quality & Timeline
* **No Artificial Timelines:** There is no rushed timeline. The singular goal is to make the best possible product.
* **Production-Level Output:** All code must be production-ready, highly scalable, highly configurable, and follow the best industry practices.

### 2. Documentation-First & Research
* **Specs Before Code:** All documentation and specifications must be up to date and preferably written *before* writing any code so the objective is perfectly clear.
* **Verify Before Acting:** If there is any doubt about an implementation, you must read the official documentation from correct sources before acting.
* **Thorough Research:** Do proper online research from credible sources. Check first-person accounts and public opinion from reliable, trustworthy sources in consideration. Document this research as much as possible.

### 3. Architecture & Design
* **Single Responsibility:** Everything should do one thing well. Build highly composable components.
* **First Principles:** Maintain strict separation of concerns and build proper abstractions derived from first principles.
* **Reuse Over Reinvention:** Reuse existing, high-quality tools and libraries instead of reinventing the wheel. Always use the correct tool and method for the correct job.

### 4. Operational Excellence
* **Loud Error Handling:** Errors must be handled transparently and loudly. Code must *never* fail quietly.
* **No Hidden Behaviors:** Code must be explicit, transparent, and predictable.
* **Infrastructure as Code:** CI/CD and Telemetry are first-class citizens and must be integrated into all architectural decisions.

### 5. Testing, Auditing, & Transparency
* **Comprehensive Testing:** Implement proper testing from the start, utilizing the correct testing tools for the specific job.
* **Phase Audits:** After every major phase of updates, conduct a full audit of both the codebase and the documentation to ensure no bugs or issues were introduced and the project has not derailed from its core objectives.
* **Highlight Incomplete Work:** Always explicitly point out to the user if there are any `TODO`s, stubs, or incomplete implementations left behind after an interaction.

## Part 2: Repository Map

Do NOT treat this root directory as a single Rust workspace. Each workspace has its own `Cargo.toml`.

* **`konf/` (Agent OS):** Independent Rust workspace. 12 crates. Products are configurations (YAML), not code. Branch: `experiments/v1`.
* **`konf-experiments/` (Experiment Logs):** Findings, roadmap, session docs. Branch: `experiments/v1`. Strategic roadmap: `experiments/ROADMAP-2026-04-09.md`.
* **`smrti/` (Graph Memory):** Independent Rust workspace. Dumb storage layer on Postgres/pgvector. No LLM calls. Branch: `main`.
* **The Bridge:** `smrti/konf-tool-memory-smrti/`.
* **`unspool/`:** Personal AI assistant (ADHD). Python/FastAPI + React. Not yet on Konf.

## Part 3: Execution Rules

* Change directories before running commands: `cd konf && cargo test --workspace` or `cd smrti && cargo test --workspace`.
* Never treat the root directory as a single Rust workspace.
* Run `cargo clippy --workspace -- -D warnings` before completing Rust work.

## Part 4: Workspace Rules

### Rust (konf, smrti)
* **Async/Tokio:** Ensure async functions are properly awaited. Do not block the tokio runtime.
* **Error Handling:** NEVER use `.unwrap()` or `.expect()`. Propagate errors using `?` and `anyhow`.

### Python & PyO3 Bindings
* **Typing:** Use strict Python type hints.
* **Tooling:** ALWAYS use `uv` for dependency management and testing (`uv run pytest`).
* **GIL Safety:** Ensure the Global Interpreter Lock is handled safely when bridging `tokio` and `asyncio`.

## Part 5: Key References

* **Architecture docs:** `konf/docs/architecture/` (authoritative specs)
* **Design principles:** `konf/docs/architecture/design-principles.md` (project constitution)
* **Experiment findings:** `konf-experiments/findings/` (10 findings from experiment 001)
* **Strategic roadmap:** `konf-experiments/experiments/ROADMAP-2026-04-09.md`
* **Status report:** `konf-experiments/experiments/STATUS-2026-04-09.md`
