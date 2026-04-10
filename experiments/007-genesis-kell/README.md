# Experiment 007: `genesis.kell` (Generation 0)

**Status:** Implementation
**Date:** 2026-04-10
**Goal:** Deploy the "Genesis Kell" as a core product within the Konf ecosystem. This kell acts as the CEO, orchestrating self-evolution and spawning child kells.

## Strategy: The Cellular Mono-Repo

Instead of a separate repository, Genesis lives in `konf/products/genesis/`. This ensures the "Kernel" and the "CEO" evolve together while allowing the company state to be deployed as a portable "Cellular Container."

### 1. The Portable Cell (Docker Compose)
Genesis is deployed using Docker Compose on a production VPS. This provides:
- **Immutability:** The VPS is just a host for the Docker engine.
- **Portability:** Copy the folder, run `docker compose up`, and the company is live.
- **Isolation:** Genesis's shell access is scoped to the container, not the host root.

### 2. Networking & Security (Tailscale Membrane)
- **Tailscale:** The VPS is joined to a private Tailscale network.
- **Hardened SSH:** The SSH daemon listens ONLY on the Tailscale IP. 
- **MCP Tunneling:** The founder connects via Gemini CLI over a secure SSH tunnel:
  `ssh root@genesis-vps "konf-mcp --product genesis"`

### 3. Secret Management (Infisical)
Genesis uses the local Infisical instance provisioned by the `init` kell. No API keys are ever committed to GitHub; they are injected into the container memory at runtime.

---

## Progress

- [x] **Phase 1: Repository Scaffolding** (Moved to `konf/products/genesis`)
- [x] **Phase 2: Configuration & Workflows** (Initial CEO DNA written)
- [x] **Phase 3: Production Manifest** (Created `docker-compose.yml` in `products/genesis`)
- [ ] **Phase 4: VPS Deployment** (Provisioning live cell)
