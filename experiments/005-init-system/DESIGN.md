# Experiment 005: The Init Kell (Phase 0 Boot)

**Status:** Research & Design
**Date:** 2026-04-09
**Goal:** Define and prototype the "PID 1" of Konf OS — a product that bootstraps the infrastructure.

---

## 1. The Core Philosophy

Konf OS follows the **Linux Kernel vs. User Space** split:
1.  **Kernel (Rust):** Agnostic, fast, and dumb. It provides "system calls" (primitive tools like `shell`, `http`, `state`).
2.  **User Space (Kells):** High-level behavioral logic. 
3.  **Init Kell (Product):** The first product to boot. It manages the environment (Docker, Databases, Secrets) using kernel primitives.

### Why separate "Init" from the Kernel?
- **Freedom:** If you don't like Docker or Infisical, you don't change the Rust code. You just change the `init` product config.
- **Single Responsibility:** The Rust kernel shouldn't know how to start a database. It should only know how to run a command that starts a database.
- **Portability:** The same kernel can run in "Local Mode" (Docker-based init) or "Cloud Mode" (managed-service init).

---

## 2. Implementation: Step-by-Step

### Step 1: Define the "Generic Secret Interface"
We establish a convention for tool names in the standard library:
- `secret:get(key)`
- `secret:set(key, value)`
- `secret:list()`

### Step 2: Create the `init` Kell (Product)
We create a new directory: `konf/products/init/`.
- **`config/services.yaml`**: The "OS Manifest" where the user declares what they want (e.g., `infisical: true`, `postgres: true`).
- **`workflows/boot.yaml`**: A workflow that reads `services.yaml` and executes the necessary `shell:exec` or `http:get` calls to provision the world.

### Step 3: The "MCP Bridge" (Pluggable Backends)
This is how we handle the "What if I don't want Infisical?" question.
- The `init` product configures an **MCP Adapter** in `tools.yaml`.
- **Scenario A (Infisical):** `tools.yaml` points the `secret:*` namespace to an Infisical MCP server.
- **Scenario B (Vault):** `tools.yaml` points `secret:*` to a Vault MCP server.
- **Scenario C (None/Local):** `tools.yaml` points `secret:*` to a simple builtin tool that reads from a `.json` file or environment variables.

### Step 4: Capability Handover
The `init` product runs, sets a flag in `state:set("sys:ready", true)`, and then the OS allows other products (assistant, devkit) to load.

---

## 3. Brainstorming: "Swapping Infisical"

How do we ensure someone can use something else or nothing at all?

### Option 1: The "No-Op" Secret Manager
If a user wants no secret manager, they set `secrets: none` in their config. The `init` kell skips provisioning, and the `secret:get` tool simply falls back to standard environment variables (`std::env::var`). 

### Option 2: The "Bring Your Own" (BYO) Manager
If a user uses 1Password or Vault:
1. They write a tiny MCP server for their provider (or use a community one).
2. They update `products/init/config/tools.yaml` to point the `secret` namespace to their MCP.
3. The rest of the Konf OS never knows the difference—it just calls `secret:get`.

### Option 3: Cloud vs. Local
- **Local:** `boot.yaml` runs `docker-compose up -d`.
- **Cloud:** `boot.yaml` calls a cloud API to verify the instance is alive.
- **Why:** The **Workflows** are the logic layer. They can branches based on environment variables like `KONF_ENV=production`.

---

## 4. Why is this better than hardcoding?

1. **Testability:** We can test the `init` workflow in a sandbox without touching the Rust kernel.
2. **Maintenance:** Updating Infisical to a new version is a 1-line change in a YAML file, not a recompilation of the OS.
3. **Founder Freedom:** As a solo founder, Bert can experiment with different infrastructure stacks (e.g., moving from Postgres to SQLite) just by swapping the `init` kell.

---

## 5. Next Actions

- [ ] Create `konf/products/init/` skeleton.
- [ ] Draft the `boot.yaml` workflow for local Docker bootstrapping.
- [ ] Implement a "Generic Secret Adapter" in the kernel that can be mapped to MCP.
