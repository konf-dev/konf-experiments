# The Konf Product Specification

**Status:** IDEA → FORMALIZATION  
**Date:** 2026-04-09  
**Origin:** Bert brain dump — epiphany about what a "product" actually is

---

## The Insight

A Konf product is a tuple:

```
Product = (konf-spec:version, kell:version)
```

Where:
- **konf-spec** = the infrastructure layer (Rust kernel, binary, tools, protocols)
- **kell** = the configuration layer (YAML, prompts, workflows, capabilities, roles)

This is the complete, formal definition of a distributable AI agent.

---

## konf-spec

The "laws of physics" — what primitives exist and how they behave.

**Contains:**
- Rust binary version (engine, runtime, builtin tools)
- Available tool traits and their interfaces
- Capability lattice semantics
- Resource limit enforcement model
- Protocol support (MCP, HTTP, SSE)
- Supported adapters (Postgres, SQLite, MCP client, etc.)

**Properties:**
- Versioned (semver)
- Source code linked and auditable
- Hardware-agnostic (runs on phone, laptop, server, cluster)
- Deterministic — same spec version, same primitive behavior

**konf-spec does NOT contain:**
- Any opinion about what agents do
- Any opinion about which LLM to use
- Any opinion about what tools to enable
- Any data

---

## kell — The Configuration Bundle

The "constitution" — what this specific product does, can do, and how it behaves.

**Contains:**
- `project.yaml` — metadata, name, version, konf-spec version dependency
- `tools.yaml` — which tools, which providers, which guards, which roles
- `models.yaml` — LLM providers and model configs
- `workflows/` — YAML DAGs defining all behavior
- `prompts/` — markdown templates for LLM instructions
- `schedules.yaml` — when autonomous workflows run
- Optional: persona configs, environment references, adapter configs

**Properties:**
- **Frozen at a point in time** — versioned, diffable, forkable
- **The only data is configuration data** — strings, paths, links, API refs, env var references
- **Hardware-agnostic** unless explicitly declared in config
- **LLM-agnostic** unless explicitly declared in config
- **Tool-agnostic** unless explicitly declared in config
- **Self-modifying** if permitted — kell can edit its own config (add workflows, tighten guards, layer monitoring)
- **Composable** — multiple kells can be merged or connected

**kell does NOT contain:**
- Application data (that's the client's responsibility)
- Compiled code
- Runtime state

---

## Data — The Client's Responsibility

Data is external to the product definition. kell specifies *how* to connect to data, not the data itself.

**Connection methods:**
- Adapters we provide (Postgres/pgvector via smrti, SQLite, etc.)
- Custom adapters the client writes and connects via MCP, HTTP, or tool trait
- File system paths
- API endpoints
- Environment variables pointing to secrets/credentials

Agents discover data sources if correctly specified in kells config. The product doesn't own data — it owns behavior.

---

## Instantiation

First time you use the product:

1. Resolve konf-spec version → get/build the correct konf binary
2. Load kell with correct version → config bundle
3. Validate kell against konf-spec (are required tools available? are capabilities valid?)
4. Boot → running product instance

All hyperparameters can be changed at runtime via config reload. The running instance reflects the current kell, not a compiled artifact.

---

## Self-Modification

If kell permits it (via capability grants), the running product can:
- Add new workflows (generate → validate → register)
- Layer security (add guard rules, monitoring agents, auth workflows)
- Adjust scheduling (add/remove/modify autonomous workflows)
- Evolve prompts (tighten or relax LLM instructions)
- Tighten its own permissions (attenuation — never amplification)

Self-modification follows the same validation-first pipeline as any workflow change. The diff between kell:v1 and kell:v2 is a config diff — auditable, reversible, explainable.

---

## Composition

Products compose in two ways:

### 1. Merge — Single kell from multiple kells
Combine configs from multiple kells into one larger kell. Can be done:
- Manually (human edits YAML)
- Via AI (workflow that merges configs with conflict resolution)
- Via tooling (future: `konf merge kell1 kell2 → kell3`)

Conflicts are resolved by explicit precedence or by the merging agent.

### 2. Connect — Multiple kells talking to each other
Each kell runs independently. They communicate via:
- MCP (Konf-to-Konf: instance A uses instance B's workflows as tools)
- HTTP (REST APIs between products)
- Shared data layer (both connect to same Postgres, same event bus)
- Any protocol the client chooses

The connection behavior is itself specified in each kells config.

### Recursive composition
kell can contain references to other kells. A "meta-product" is just a kell that composes sub-kells. This is already how devkit works — it's a kell that governs the development of other kells.

---

## Versioning & Distribution

```
my-product/
├── konf.lock              # Resolved konf-spec version + dependency hashes
├── project.yaml           # name, version, konf-spec dependency
├── config/
│   ├── tools.yaml
│   ├── models.yaml
│   ├── schedules.yaml
│   └── workflows/
│       └── *.yaml
└── prompts/
    └── *.md
```

**Version control:** Git. Every kell change is a commit. Forks are git forks. Composition is git merges or submodules.

**Distribution:** A tarball, a git repo, a directory. `konf run ./my-product/` boots it. Like `docker run` but for AI agents.

---

## Philosophy Alignment

| Principle | How it applies |
|-----------|---------------|
| Single responsibility | kell does one thing. Compose for more. |
| Kernel minimalism | konf-spec is minimal. Behavior lives in kell. |
| Prompts over code | kell is prompts + config, not compiled code. |
| Everything composable | kell composes with kell. Tools compose in workflows. Workflows compose as tools. |
| Config as constitution | kell IS the constitution. Amend it via versioned config updates with due process. |
| Capability attenuation | Self-modification can only tighten, never escalate. |
| Everything observable | kell changes are diffs. Runtime behavior is journaled. |

---

## What This Means for the Market

- **For developers:** Fork a kell, edit YAML, run. No code. Publish your kell for others.
- **For enterprises:** Audit kell like a contract. Every behavior is in config. Every change is a diff.
- **For the ecosystem:** An app store of kells. Rate them, fork them, compose them.
- **For Konf:** konf-spec is the platform. kells are the ecosystem. We maintain the kernel. The community builds the products.

---

## Design Philosophy

Self-modification is powerful. That's the point. Risk tolerance is defined differently for everyone — they mitigate it how they see fit, like any programming language or OS. We provide the primitives, prove they work through real use cases, and let people build what they want within their own boundaries.

Not our job to decide what's "safe enough" for other people. The capability lattice is the mechanism. Tool guards are configurable. What people do within their own scope is their business. `rm -rf /` exists in Linux. People learn. And if they want a guard, they configure one.

---

## Etymology

The name **kell** comes from the **Kell calculus** (Schmitt & Stefani, 2003-2007) — a formal model for distributed systems where a "kell" is a *named computational boundary* that encapsulates processes and controls communication across its membrane. This is precisely what a Konf kell is: a named boundary of configuration that defines what processes run, what they can do, and how they communicate.

The Kell calculus is part of the process algebra family (alongside CSP, pi-calculus) and provides formal semantics for exactly the kind of hierarchical, capability-controlled, composable boundaries that Konf implements.

---

## Open Questions

1. **Lock file format:** How to pin konf-spec version + tool versions + adapter versions deterministically.
3. **Composition semantics:** When two kells have conflicting guards or capabilities, what wins?
4. **Distribution registry:** Eventually, where do kells live? Git repos? A registry like crates.io?
5. **Migration:** When konf-spec bumps a major version, how do kells migrate?
