# Experiment 007: `genesis.kell` (Generation 0)

**Status:** Implementation (Updated Strategy)
**Date:** 2026-04-10
**Goal:** Deploy the "Genesis Kell" as a core product within the Konf ecosystem. This kell acts as the "Second-in-Command" and Sandbox Orchestrator, providing a single, powerful point of contact via MCP.

## Strategy: The Second-in-Command Sandbox

Instead of a "CEO" or a "Triad," Genesis is a highly capable generic foundational agent. It provides a sandboxed environment executing Konf and offering MCP access.

### 1. The Power Set (LiteLLM + Ollama + OpenCode Zen)
Genesis is powered by a flexible LLM stack:
- **Local/Free Inference:** Ollama proxied through LiteLLM.
- **Advanced Reasoning:** OpenCode Zen configured for complex tasks.
- **Remote Control:** Accessed via remote OpenCode over a secure Tailscale network.

### 2. The Persona (Second-in-Command)
Genesis is the Founder's right-hand entity. It executes tasks, manages the sandbox, and helps spawn specialized agents with explicit personas only when needed.

### 3. The Membrane (Podman + Tailscale + Infisical)
- **Podman:** Genesis's opinion is to use Podman for rootless, daemonless containerization.
- **Tailscale:** Zero public attack surface; accessible only via private mesh network.
- **Infisical:** Secure secret injection without disk exposure.

---

## Progress

- [x] **Phase 1: Repository Scaffolding** (Integrated in `konf/products/genesis`)
- [x] **Phase 2: Configuration & Workflows** (Updated to Second-in-Command strategy)
- [x] **Phase 3: Production Manifest** (Created `docker-compose.yml` in `products/genesis`)
- [ ] **Phase 4: VPS Deployment** (Provisioning live cell)
