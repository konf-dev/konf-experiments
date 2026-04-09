# Konf: Grand Plan — A Viable System for AI Agents

**Date:** 2026-04-09
**Status:** Research complete, synthesized from 30+ cross-disciplinary concepts
**Author:** Claude Code, synthesized from three parallel deep research agents + Bert's brain dump + architecture docs

---

## Context

Bert is building Konf — an operating system for AI agents where products are configurations (YAML), not code. The engine (Rust) provides mechanisms; workflows provide policies. Everything is composable, observable, and forkable. E2 (agent-mode ai_complete with capability-enforced ReAct loops) just completed, meaning the kernel now enforces its capability model end-to-end through LLM tool-use loops.

The goal of this document: define what Konf IS at the deepest level, using cross-disciplinary research to identify the correct abstractions. Not an implementation plan — a constitution. The system below the Broker is deliberately left open-ended; what emerges depends on real usage, not top-down design.

---

## Part 1: What Konf IS — The Theoretical Identity

### The One-Sentence Definition

**Konf is a recursive viable system that coordinates AI agents through stigmergic communication, capability-attenuated security, and configuration-as-constitution — enabling self-producing, self-regulating, self-improving organizations of agents that gradually internalize human decision patterns.**

Every word in that sentence is grounded in established theory:

| Term | Field | Source |
|------|-------|--------|
| Recursive viable system | Management Cybernetics | Stafford Beer, VSM (1972) |
| Stigmergic communication | Biology / Swarm Intelligence | Grasse (1959), Dorigo (2004) |
| Capability-attenuated security | Computer Science | Dennis & Van Horn (1966), Mark Miller (2006) |
| Configuration-as-constitution | Political Theory / AI Safety | Ostrom (1990), Anthropic Constitutional AI (2022) |
| Self-producing | Systems Biology | Maturana & Varela, Autopoiesis (1972) |
| Self-regulating | Cybernetics | Wiener (1948), Ashby (1956) |
| Self-improving | Organizational Learning | Argyris, Double-Loop Learning (1977) |
| Internalize human decision patterns | Educational Psychology | Vygotsky, ZPD/Scaffolding (1978) |

---

## Part 2: The Seven Abstractions

From 30+ concepts across 12 fields, seven fundamental abstractions recur. These are not Konf-specific — they are the universal properties of viable self-organizing systems. Konf's job is to provide mechanisms for each.

### Abstraction 1: Stigmergic Coordination (The Event Bus)

**What it is:** Agents coordinate indirectly by modifying a shared environment. No central router. No direct agent-to-agent messaging required.

**Theoretical grounding:**
- **Stigmergy** (Grasse, 1959) — ants coordinate through pheromone trails left in the environment, not through direct communication. The environment IS the coordination mechanism.
- **Hayek's Knowledge Problem** (1945) — the knowledge needed to allocate resources is dispersed across agents and cannot be centralized. Prices (events) communicate distributed information. Central planning (push-based orchestration) fails because it requires omniscient knowledge.
- **Global Workspace Theory** (Baars, 1988) — consciousness emerges when specialist processors broadcast to a shared workspace. The workspace is the coordination medium; specialists compete for access.
- **Actor Model** (Hewitt, 1973) — actors communicate only via asynchronous messages. No shared mutable state.

**What this means for Konf:**
- The event bus is the shared environment — digital pheromone trails
- Events are published, not routed. The publisher doesn't know who will consume them
- Agents self-select from the bus based on local knowledge (Hayek's "man on the spot")
- Push-based orchestration = central planning. Poll-based = market mechanism
- The event bus should be an append-only log (event sourcing), not transient pub/sub — enabling replay, audit, and temporal queries
- Event TTL / decay maps to pheromone evaporation — old signals lose relevance

**Key design principle:** Never build a centralized router that tries to understand all agent capabilities. Publish events and let agents bid/claim based on their local knowledge.

---

### Abstraction 2: Capability Attenuation (The Immune System)

**What it is:** Authority flows by delegation and can only be reduced, never amplified. The system defines "self" (permitted operations) and rejects "non-self."

**Theoretical grounding:**
- **Capability-Based Security** (Dennis & Van Horn, 1966; Miller, 2006) — a capability is an unforgeable token that both identifies a resource AND defines permitted operations. No ambient authority. Only connectivity begets connectivity.
- **Immune System Negative Selection** (Forrest, 1994) — T-cells that react to self-antigens are destroyed. Only cells that DON'T match self survive. This is "learning what is normal."
- **Matzinger's Danger Theory** (1994) — don't just check permissions (self/non-self); also watch for contextual danger signals (anomalous behavior patterns).
- **Separation of Powers** (Montesquieu, Madison) — no single entity holds all power. An agent that can define its own permissions, execute its own code, AND judge its own performance is a tyrant agent.

**What this means for Konf:**
- ExecutionScope IS the capability model. Child scopes can only attenuate, never amplify
- Permission checking alone is insufficient — add danger signals (anomalous behavior patterns, not just rule violations)
- Proportional response tiers: log → alert → throttle → kill → revoke capability
- The kernel (Rust) IS the constitution — immutable mechanisms that constrain what workflows can do
- Separation: runtime enforces limits (executive), workflows define policy (legislative), philosopher resolves conflicts (judicial)

**Key design principle:** "The accumulation of all powers in the same hands may justly be pronounced the very definition of tyranny" (Madison). No agent defines its own permissions.

---

### Abstraction 3: Recursive Viability (The Organizational Skeleton)

**What it is:** Every viable subsystem contains the same five functions, at every scale. This is the closest existing model to Konf's architecture.

**Theoretical grounding:**
- **Stafford Beer's Viable System Model** (1972) — any system that maintains independent existence in a changing environment must have five subsystems, and this structure is recursive:
  - **S1 Operations** — units that do actual work, each autonomous
  - **S2 Coordination** — prevents oscillation between S1 units (scheduling, protocols, namespace isolation)
  - **S3 Control** — manages resources across S1 units, balances autonomy vs cohesion. Includes **S3\* Audit** — sporadic direct investigation
  - **S4 Intelligence** — scans the external environment, models possible futures
  - **S5 Policy/Identity** — defines purpose, values, resolves tension between S3 (optimize now) and S4 (adapt for future)
- **Ashby's Law of Requisite Variety** (1956) — a controller must have at least as much variety as the system it controls. Konf's approach: reduce operational variety through structural constraints rather than building an omniscient controller.

**VSM mapped to Konf:**

| VSM System | Konf Equivalent | Crate/Mechanism |
|-----------|-----------------|-----------------|
| S1 Operations | Workflows doing actual work | `konflux` engine |
| S2 Coordination | Event bus protocols, namespace isolation, resource limits, scheduling | `konf-runtime` (ExecutionScope, ResourceLimits) |
| S3 Control | Process table, runtime metrics, event journal, zombie reconciliation | `konf-runtime` (process management) |
| S3\* Audit | Audit workflows that bypass normal flow to directly inspect state | Configurable — a workflow pattern |
| S4 Intelligence | Proactive poll-based agents, external MCP connections, pattern detection | `konf-tool-mcp`, proactive workflows |
| S5 Policy/Identity | Product config, capability lattice, philosopher agent | Product config directory |
| Recursion | Products contain workflows contain steps — each level has its own S1-S5 | `child_scope()` attenuation |
| Algedonic signals | Critical error events that bypass normal priority to reach S5 immediately | Event bus fast-path (to be designed) |

**What this means for Konf:**
- Don't prescribe organizational structure (offices, departments) — but ensure the five VSM functions exist at every level of recursion
- The missing pieces: S2 coordination (preventing workflow interference beyond namespace isolation), S3\* audit (sporadic direct investigation channel), algedonic signals (fast-path for emergencies)
- The capability lattice IS variety engineering — reduce what the controller must handle rather than making the controller more complex
- Beer's autonomy-cohesion tension maps directly: workflows have full autonomy within their scope, but the scope is set by the level above

**Key design principle:** Every level of the system (tool, workflow, product, platform) must have operations, coordination, control, intelligence, and policy. Missing any = fragile.

---

### Abstraction 4: Self-Production (Autopoiesis)

**What it is:** The system produces and maintains its own components, boundary, and identity from within.

**Theoretical grounding:**
- **Autopoiesis** (Maturana & Varela, 1972) — an autopoietic system is "a network of processes of production of components which through their interactions continuously regenerate the network that produced them." Organization (abstract relations) is preserved even as structure (material realization) changes.
- **Luhmann's Social Systems** (1984) — social systems are made of communications, not people. Communications produce more communications. The system is operationally closed.
- **Ship of Theseus** — identity persists through continuity of form, not continuity of material. Every workflow can be replaced; the product is "the same product" as long as its purpose and structure persist.

**What this means for Konf:**
- A Konf product is autopoietic when its workflows can create/modify other workflows, maintain config integrity, and define its own boundary via capabilities
- The system's "communications" are tool calls — the atomic unit. Tool calls produce more tool calls (workflow composition). The event journal records all communications.
- The user is environment, not component — they send messages in, but the system's internal processing is self-contained (operational closure)
- Forkable config = preserving organization while varying structure. A fork is a new system with the same organizational pattern but different structure.
- Config IS the identity — the product's `project.yaml` + workflow directory defines what it IS, not what it's currently doing

**Key design principle:** The system must be able to produce its own new components from existing components (generate → validate → register). Self-modification is the mechanism of autopoiesis.

---

### Abstraction 5: Predictive Self-Regulation (Active Inference)

**What it is:** The system maintains itself by minimizing the difference between its model of how things should be and how things actually are.

**Theoretical grounding:**
- **Free Energy Principle / Active Inference** (Friston, 2006) — any self-organizing system that persists must minimize surprise. Two ways: update your model (perception/learning) or change the world (action).
- **Homeostasis** (Cannon, 1926) — negative feedback loops maintain variables within operating envelopes.
- **Allostasis** (Sterling, 1988) — predictively adjust setpoints based on anticipated demands, not just react to deviation.
- **Kubernetes Reconciliation Loops** — declare desired state, observe actual state, compute delta, take corrective action, repeat. Level-triggered, not edge-triggered.

**What this means for Konf:**
- YAML config = the generative model ("this is how things should be")
- Metrics and events = observations of actual state
- Prediction error = deviation between config expectations and runtime reality
- Self-healing has two modes: update config (the model was wrong) or execute corrective workflows (make reality match the model)
- The philosopher agent decides which mode — this is the deepest function of S5
- Proactive agents are reconciliation loops: observe desired state (config) → observe actual state (events) → compute delta → act → repeat
- This is level-triggered (continuously check "is the world right?") not edge-triggered (react once to a specific event)

**Key design principle:** Self-healing is active inference. The system should never be "surprised" by its own state. When it is, it either updates its beliefs or acts to correct reality.

---

### Abstraction 6: Process Distillation (Scaffolding Withdrawal)

**What it is:** The system gradually internalizes human decision patterns, reducing the need for human intervention over time.

**Theoretical grounding:**
- **Vygotsky's Zone of Proximal Development** (1978) — scaffolding is temporary support that must be systematically withdrawn as competence grows. The goal is autonomy and internalization, not permanent dependency.
- **Cognitive Apprenticeship** (Collins, Brown & Newman, 1989) — learning through doing alongside an expert. Modeling → coaching → scaffolding → articulation → reflection → exploration (independence).
- **Double-Loop Learning** (Argyris, 1977) — single-loop: detect error → change action. Double-loop: detect error → question governing assumptions → change goals/norms. Triple-loop: change the rules for changing rules.
- **Kaizen** (Ohno, Imai) — small continuous improvements by the people doing the work, not big top-down redesigns.

**What this means for Konf:**
- The human (Bert) starts as the expert, the system as the apprentice
- Every decision the human makes is recorded (event bus → smrti)
- Over time, patterns are distilled into config — the system replaces human decisions with automated ones, one at a time
- The boundary moves gradually: verify competence at each step before withdrawing more scaffolding
- Single-loop = catch/retry. Double-loop = philosopher questioning workflow goals. Triple-loop = modifying the rules for modifying itself.
- This IS kaizen — small continuous improvements to config, not revolutionary redesigns

**Key design principle:** Move the boundary of autonomy gradually. The system gets more capable not through big releases but through continuous distillation of observed patterns.

---

### Abstraction 7: Constitutional Governance (Configuration as Law)

**What it is:** Explicit, inspectable, forkable rules that govern agent behavior — not opaque training or prompt engineering.

**Theoretical grounding:**
- **Ostrom's Polycentric Governance** (1990, Nobel 2009) — communities can effectively manage shared resources without centralization. Her 8 design principles for stable commons map almost 1:1 to Konf:
  1. Clearly defined boundaries → ExecutionScope
  2. Rules fit local circumstances → Product config per product
  3. Participatory decision-making → Config is forkable; authors write their own rules
  4. Monitoring/accountability → Health tracking, event journal
  5. Graduated sanctions → ResourceLimits + throttle before kill
  6. Low-cost conflict resolution → Philosopher agent
  7. Right to self-organize → Workflows can register as tools, compose freely
  8. Nested within larger networks → Onion layers
- **Constitutional AI** (Anthropic, 2022) — govern AI behavior through explicit, inspectable constitutions rather than opaque preference models.
- **Subsidiarity** (Pius XI, 1931; EU Treaty Art. 5) — decisions at the lowest competent level. Higher authority intervenes only when lower level cannot handle it.
- **Weber's Bureaucracy** — hierarchy, rules, specialization, documentation. Konf implements Weberian strengths while avoiding the "iron cage" through forkable config and self-healing.

**What this means for Konf:**
- Product config IS a constitution. It can be read, understood, modified, and forked.
- "One size fits all" policies don't work (Ostrom) — everything is configurable per product
- Subsidiarity: workflows handle their own domain; escalation only on failure
- The philosopher agent implements Constitutional AI at runtime — evaluating competing claims against the product's explicit principles
- Weber's warning is a design requirement: the system MUST avoid bureaucratic pathology. Rules should have clear purpose; self-auditing should detect when rules become dysfunctional.

**Key design principle:** Transparent governance over opaque control. Every rule is inspectable, every decision is auditable, every config is forkable.

---

## Part 3: The Convergence Proof

The strongest validation of Konf's architecture is that three completely unrelated fields independently converge on the same pattern for each core concept:

| Konf Concept | Field 1 | Field 2 | Field 3 |
|---|---|---|---|
| Poll-based agents | Hayek (economics — distributed knowledge) | Actor Model (CS — message passing) | K8s operators (infrastructure — reconciliation) |
| Event bus | Stigmergy (biology — pheromone trails) | GWT (neuroscience — global workspace) | Event sourcing (CS — append-only log) |
| Capability model | Immune system (biology — negative selection) | Capability security (CS — unforgeable tokens) | Separation of powers (governance — no concentrated authority) |
| Self-healing | Active inference (neuroscience — minimize surprise) | OTP supervision (CS — let it crash) | Homeostasis (physiology — feedback loops) |
| Process distillation | ZPD scaffolding (education — gradual withdrawal) | Kaizen (manufacturing — continuous improvement) | Double-loop learning (org theory — questioning assumptions) |
| Config as constitution | Ostrom's commons (governance — design principles) | Constitutional AI (AI safety — explicit rules) | Autopoiesis (biology — organization preserved across structural change) |
| Recursive structure | VSM (cybernetics — five systems at every scale) | Fractal geometry (math — self-similarity) | Holacracy (org design — nested circles) |

When three unrelated disciplines converge on the same pattern, the pattern is fundamental.

---

## Part 4: What the Kernel Must Provide (Mechanisms)

These are the Rust-level primitives. They enable all seven abstractions without prescribing how they're used.

| Mechanism | Enables Abstraction | Already Exists? |
|-----------|-------------------|-----------------|
| Event bus (broadcast + append-only log) | Stigmergic coordination | Partially (EventJournal is write-only; no broadcast) |
| Capability lattice (attenuate only) | Immune system security | Yes (ExecutionScope, CapabilityGrant) |
| Recursive scoping (child_scope) | Recursive viability | Yes |
| Workflow execution (DAG engine) | All — workflows are the universal unit | Yes (konflux engine) |
| Tool trait (universal interface) | Composability | Yes |
| Resource limits (cgroup model) | Self-regulation, variety reduction | Yes (ResourceLimits) |
| Health tracking (per-tool metrics) | Self-observation | Yes (RuntimeMetrics) |
| Process lifecycle (spawn/wait/cancel/kill) | Self-production, self-healing | Yes (konf-runtime) |
| Config hot-reload | Constitutional governance | Yes (config_reload, ArcSwap) |
| Namespace injection | Boundary definition | Yes (VirtualizedTool) |

**What's missing (kernel-level):**
1. **SystemEventBus** — `tokio::sync::broadcast` with filtered subscriptions and capability-scoped redaction
2. **Algedonic fast-path** — critical events bypass normal priority to reach S5 immediately
3. **Poll subscription primitive** — configurable in YAML: what to watch, how often, what to trigger
4. **Event priority/salience** — dual mechanism: top-down (policy bias) + bottom-up (event urgency)

Everything else is workflow-level (policy), not kernel-level (mechanism).

---

## Part 5: What Emerges from Config (Policies)

These are NOT kernel features. They are workflow patterns that someone (Bert, the system, a community member) creates using the mechanisms above. The system doesn't know about them. They're just YAML.

| Pattern | What It Is | VSM System |
|---------|-----------|------------|
| Oversight workflow | Intercepts actions, checks against a policy config | S3 Control |
| Audit workflow | Sporadically inspects subsystem state directly | S3\* Audit |
| Philosopher workflow | Resolves conflicts between competing workflows with broader context | S5 Policy |
| Health check workflow | Reconciliation loop: desired state vs actual state | S4 Intelligence |
| Memory consolidation | Periodically reviews event journal, compresses, summarizes | S2 Coordination |
| Process distiller | Analyzes human decision patterns, proposes config changes | S4 Intelligence |
| Danger detector | Watches for anomalous behavioral patterns (not just rule violations) | S3 Control |
| The Broker | Parses human intent, publishes to bus, surfaces escalations | S5 Policy interface |

None of these are hardcoded. All of them are forkable. Someone might build completely different patterns. That's the point.

---

## Part 6: The Feedback Loop — From Human to Autonomous

This is Vygotsky's scaffolding withdrawal applied concretely:

```
Phase 0: Human does everything, system records
         ↓
Phase 1: System suggests, human approves/rejects
         ↓ (decisions recorded, patterns emerge)
Phase 2: System handles routine decisions, escalates novel ones
         ↓ (more patterns distilled)
Phase 3: System handles most decisions, human reviews daily digest
         ↓ (philosopher agent resolves most conflicts)
Phase 4: Human sets constraints, approves 0-3 escalations/day, brain dumps new ideas
```

Each phase transition requires verified competence at the current level. The system doesn't advance to Phase 2 until Phase 1 patterns have been validated. This is ZPD — never move faster than the demonstrated capability supports.

---

## Part 7: The Product Architecture

### What Ships

Three things. Each is independent but they compose.

#### Product 1: `konf` (The Engine)

**What it is:** A single Rust binary. You download it, point it at a config directory, run `konf run products/my-thing/`. That's it. Your agents are running.

**What's inside:**
- Workflow engine (DAG executor)
- Tool registry (HTTP, LLM, shell, memory, MCP bridge, custom)
- Capability-attenuated security (the immutable law)
- Event bus (stigmergic coordination)
- Process manager (spawn/wait/cancel/kill, resource limits)
- Config hot-reload (change YAML, agents adapt live)
- MCP server (any MCP client can connect — Claude Code, Cursor, etc.)
- HTTP API (REST + SSE streaming, JWT auth)
- Scheduler (cron-style triggers)

**What's NOT inside:**
- No UI. No dashboard. No frontend. Just the engine.
- No specific agent patterns (no broker, no philosopher — those are config)
- No opinion on what you build

**How you get it:**
- `curl -sSf https://konf.dev/install.sh | sh` (single binary, Linux/macOS/Windows)
- `docker pull konf/konf` (container)
- Build from source (`cargo build --release`)

**License:** BSL-1.1 (source-available, converts to Apache 2.0 after 4 years)
- Free for: single-organization self-hosted, personal use, non-competitive forks
- Paid: multi-tenant SaaS, white-label resale, embedded in competing product

**This is the Proxmox model:** Full-featured, no artificial limitations. You pay for the platform (Product 3), not the engine.

---

#### Product 2: `konf-registry` (The Ecosystem)

**What it is:** A registry of forkable configs. Like Docker Hub for agent setups.

**What you find there:**
- **Products** — complete agent sandboxes (e.g., "ADHD personal assistant", "code review bot", "customer support agent")
- **Workflows** — reusable patterns (e.g., "research-then-summarize", "code-review-pipeline", "daily-digest")
- **Tool bundles** — curated tool sets for specific domains (e.g., "web-scraping-tools", "devops-tools")
- **Prompt libraries** — tested, versioned prompts
- **Governance presets** — oversight configs for different risk profiles (e.g., "full-autonomy", "human-in-the-loop", "enterprise-compliance")

**How it works:**
- `konf registry search "personal assistant"` → list of matching products
- `konf registry fork user/adhd-assistant ./my-assistant` → copies their config locally
- `konf registry publish ./my-product` → shares your config
- Public by default. Private registries for paid tier.

**Quality signals:**
- Fork count, star count (like GitHub)
- Verified badge for tested configs
- Automated quality checks (valid YAML, no hardcoded secrets, capability analysis)

**Monetization:** Free public publishing. Paid private registries (teams/enterprise).

**This is the Docker Hub model:** Free distribution drives adoption. Monetize private/enterprise features.

---

#### Product 3: `konf.dev` (The Platform)

**What it is:** A hosted platform where you sign up, create a project, and get a running Konf instance — no DevOps required.

**What the user sees:**

1. **Project dashboard** — list of projects, each with status (running/stopped), agent count, quick actions (start/stop/fork/delete)
2. **Chat interface** — talk to your agents via SSE/WebSocket. This is the primary interaction surface.
3. **Config editor** — view/edit YAML in browser. Visual workflow graph (read-only, generated from YAML — YAML is source of truth). Live config reload.
4. **Observability** — process table, live event stream, health dashboard (per-tool metrics), capability tree (who can do what)
5. **Registry browser** — discover, fork, and publish configs without leaving the platform

**What's under the hood:**
- Each project = isolated container running `konf` engine
- Container provisioned on sign-up, destroyed on project delete
- Storage: PostgreSQL (smrti for memory), S3-compatible for artifacts
- Compute: configurable (bring your own LLM keys, or use platform-provided models)

**Pricing (Proxmox-inspired):**

| Tier | Price | What You Get |
|------|-------|-------------|
| Free | $0 | 1 project, 1 sandbox, community registry, bring your own LLM keys |
| Pro | $19/mo | 5 projects, private registry, priority scheduling |
| Team | $49/mo | Unlimited projects, team collaboration, shared registries, audit logs |
| Enterprise | Custom | Self-hosted support, SLA, SSO/LDAP, custom deployment |

**Key principle:** The self-hosted engine is IDENTICAL to what runs on the platform. No feature gating. The platform charges for operational convenience — provisioning, monitoring, backups, team management. Not for features.

---

### How They Compose

```
Self-hoster:
  Downloads konf binary → runs locally → browses registry → forks configs → done

Platform user:
  Signs up at konf.dev → creates project → forks from registry → talks to agents → done

Both paths use the same engine, same config format, same registry.
The engine doesn't know if it's self-hosted or on the platform.
```

**No vendor lock-in.** Export your entire project as a tarball. Run it anywhere. It's YAML — read it with cat. Self-hosted works offline, air-gapped. No mandatory cloud dependency. No LLM lock-in — bring your own keys, use ollama locally, use any provider.

---

### Layer 2: The Social Platform

NOT a separate product. It's a Konf product ON the platform — a config in the registry.

**What it enables:**
- Multi-user chat rooms backed by Konf instances
- Project pitching (users propose ideas, agents + humans discuss)
- Project selection (configurable rules — votes, metrics, community consensus)
- Dedicated project forums with their own Konf config
- AI + human moderation (governance workflows, configurable per forum)
- Organic growth (fork successful projects, remix, share back)

**Why it's not built first:** It needs the platform to exist and a community to have something to collaborate on. It emerges when the registry has enough interesting configs that people want to build together.

**Why it's powerful:** Every project forum IS a Konf instance. The collaboration infrastructure is the same as the agent infrastructure. There's no separate "social layer" — it's agents all the way down.

---

### Go-to-Market: Products First, Platform Second (NEEDS MORE BRAINSTORMING)

**The Shopify insight:** Don't sell the infrastructure. Sell products built on it.

Each Konf product is advertised independently — its own brand, landing page, audience, value prop. Users don't need to know what Konf is. They just use the product.

**Example products (each is a config directory in the registry):**
- **unspool** — "AI assistant for people with ADHD" — powered by konf
- A code review product — "AI reviewer that learns your team's standards" — powered by konf
- A research assistant — "Reads papers, summarizes, connects ideas" — powered by konf
- A devops agent — "Manages your deployments and alerts you when things break" — powered by konf
- Whatever the community builds

**What this achieves:**
- Parallel experiments on product-market fit without betting on one vertical
- Each product drives awareness back to konf.dev ("How was this built?" → developer discovers the platform)
- End users get a product; developers get a platform. Both win.
- "Powered by konf" becomes a trust signal, not a sales pitch

**The two audiences:**

| Audience | What they see | What they care about |
|----------|--------------|---------------------|
| End users | Individual products (unspool, code reviewer, etc.) | "Does this solve my problem?" |
| Builders | konf.dev — the platform | "Can I build my own agent product on this?" |

**Positioning:**
- Products: whatever the product's audience needs to hear
- Platform (konf.dev): "The OS for AI agents. Products are configurations, not code."
- The depth (VSM, stigmergy, capability security, civilization) reveals itself as builders go deeper — you don't lead with it

**Frontends are just consumers of the engine:**
- CLI/TUI — for developers in terminal (ships first, fastest to build)
- konf.dev web dashboard — for broader adoption
- MCP clients — Claude Code, Cursor, any IDE (already works today)
- Mobile app — possible via HTTP API
- Custom frontends — any framework against the SSE stream

**Open questions (need brainstorming):**
- Which product to launch first? unspool is the obvious candidate (it exists, it's personal, it's Bert's own use case)
- How many products to run in parallel vs. focus on one?
- Should "powered by konf" be visible from day one, or only revealed once a product has traction?
- How to handle the transition when an end user wants to go deeper and become a builder?
- Pricing: per-product or per-platform? Can end users pay for unspool without knowing about konf?
- What's the first product that could generate revenue?

---

## Part 8: What We Don't Prescribe

Everything below the Broker is hypothesis. The research provides vocabulary and patterns, but the actual organizational structure that emerges depends on:

- What Bert asks the system to do
- What works and what fails in practice
- What patterns get distilled from real usage
- What the community discovers

The kernel provides: scoping, capability attenuation, event streaming, memory, tool execution, workflow composition. **What people build with those primitives is not predetermined.**

The only structural guarantee: whatever emerges, it's bounded by the capability model (can't exceed parent permissions), auditable (events are recorded), and configurable (it's all YAML you can read, fork, or delete).

---

## Part 9: Key Warnings from the Research

### From Weber: Avoid the Iron Cage
Bureaucratic rules originally designed as means can become ends in themselves. Every rule in Konf must trace to a user outcome. Self-auditing must detect when rules become dysfunctional.

### From Kauffman: Avoid Freezing or Chaos
The capability lattice is a tuning dial. Too restrictive = frozen, no emergence. Too permissive = chaos, no coherence. The philosopher's deepest job: maintain the system at the productive edge of chaos.

### From Ashby: No Omniscient Controller
A single oversight agent CANNOT regulate complex multi-agent behavior. Multi-layer feedback at every level is required. This is why VSM recursion matters.

### From Deming: Don't Overreact to Normal Variation
Distinguish common-cause variation (normal LLM latency) from special-cause (API outage). Resource limits should be set based on expected variation, not worst-case.

### From the DMN: Protect Background Processing
Never let active task execution consume 100% of resources. Reserve capacity for monitoring, auditing, planning, and consolidation. The "idle" agents are doing the most important work.

### From Matzinger: Permission Checks Are Not Enough
A workflow might have permission to delete files, but if it's deleting an unusual number + CPU is spiking + it's 3 AM = danger signal. Context-aware escalation, not just rule-based.

---

## Part 10: Research Sources — Full Bibliography

### Biology & Complex Systems
- Grasse, P.P. (1959). "La reconstruction du nid et les coordinations interindividuelles"
- Maturana, H. & Varela, F. (1972). "Autopoiesis and Cognition" — Springer
- Dorigo, M. & Stutzle, T. (2004). "Ant Colony Optimization" — MIT Press
- Forrest, S. et al. (1994). "Self-nonself discrimination in a computer" — IEEE S&P
- Matzinger, P. (1994). "Tolerance, Danger, and the Extended Family" — Annual Review of Immunology
- Simard, S.W. et al. (1997). "Net transfer of carbon between ectomycorrhizal tree species" — Nature 388
- Holland, J.H. (1995). "Hidden Order: How Adaptation Builds Complexity" — Addison-Wesley
- Kauffman, S. (1995). "At Home in the Universe" — Viking
- Somayaji, A. et al. (1997). "Principles of a Computer Immune System" — NSPW

### Neuroscience
- Baars, B.J. (1988). "A Cognitive Theory of Consciousness" — Cambridge
- Desimone, R. & Duncan, J. (1995). "Neural mechanisms of selective visual attention" — Annual Review of Neuroscience
- Raichle, M.E. et al. (2001). "A default mode of brain function" — PNAS
- Friston, K. (2010). "The free-energy principle: a unified brain theory?" — Nature Reviews Neuroscience
- Parr, T. et al. (2022). "Active Inference" — MIT Press (Open Access)

### Cybernetics
- Wiener, N. (1948). "Cybernetics" — MIT Press
- Ashby, W.R. (1956). "An Introduction to Cybernetics" — Chapman & Hall
- Beer, S. (1972). "Brain of the Firm" — Allen Lane
- Beer, S. (1979). "The Heart of Enterprise" — Wiley
- Bateson, G. (1972). "Steps to an Ecology of Mind"
- Von Foerster, H. (1960s-70s). Second-order cybernetics

### Governance & Economics
- Hayek, F.A. (1945). "The Use of Knowledge in Society" — American Economic Review
- Coase, R. (1937). "The Nature of the Firm" — Economica
- Ostrom, E. (1990). "Governing the Commons" — Cambridge
- Hurwicz, L. (1960). "Optimality and Informational Efficiency in Resource Allocation"
- Benkler, Y. (2006). "The Wealth of Networks" — Yale
- Montesquieu (1748). "The Spirit of the Laws"
- Weber, M. (1922). "Economy and Society"

### Computer Science
- Dennis, J. & Van Horn, E. (1966). "Programming Semantics for Multiprogrammed Computations" — MIT
- Hewitt, C. et al. (1973). "A Universal Modular ACTOR Formalism" — IJCAI
- Miller, M. (2006). "Robust Composition" — PhD thesis
- Armstrong, J. (2003). "Making Reliable Distributed Systems in the Presence of Software Errors" — PhD thesis
- Watson, R. et al. (2010). "Capsicum: Practical Capabilities for UNIX" — USENIX Security
- Young, G. (2010). "CQRS Documents"

### Education & Psychology
- Vygotsky, L. (1978). "Mind in Society" — Harvard
- Argyris, C. (1977). "Double Loop Learning in Organizations" — HBR
- Collins, A. et al. (1989). "Cognitive Apprenticeship" — Knowing, Learning, and Instruction
- Piaget, J. (1937). "The Construction of Reality in the Child"

### Sociology & Organizational Theory
- Luhmann, N. (1984). "Social Systems"
- Weick, K.E. (1995). "Sensemaking in Organizations"
- Weick, K.E. & Sutcliffe, K.M. (2001). "Managing the Unexpected" (HROs)
- Conway, M. (1967). "How Do Committees Invent?" — Datamation

### Process & Quality
- Deming, W.E. (1993). "The New Economics" — MIT Press
- Ohno, T. (1978). "Toyota Production System"

### Philosophy
- Wittgenstein, L. (1953). "Philosophical Investigations"
- James, W. (1907). "Pragmatism"
- Dewey, J. (1938). "Logic: The Theory of Inquiry"

### AI
- Bai, Y. et al. (2022). "Constitutional AI: Harmlessness from AI Feedback" — Anthropic (arXiv:2212.08073)

---

*This document is the constitutional foundation for Konf. It defines what the system IS, not how it's implemented. Implementation evolves; identity persists.*
