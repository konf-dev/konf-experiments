# Konf Experiments

Testing and validation for the [Konf Agent OS](https://github.com/konf-dev/konf).

This repo is where we:
1. Test the architect agent against real Konf infra
2. Build products via AI-steered conversation
3. Document friction points that feed back into Konf's roadmap
4. Track what needs to be built in the next versions

## Setup

```bash
# 1. Build Konf (in the konf repo)
cd ../konf-dev-stack/konf
cargo build --release --bin konf-mcp

# 2. Start the sandbox
docker compose -f sandbox/docker-compose.yml up -d

# 3. Connect your AI client via MCP
# See experiments/ for specific experiment setups
```

## Structure

```
konf-experiments/
├── backlog/              # What needs building in Konf next
│   └── ROADMAP.md        # Prioritized list from experiment findings
├── experiments/          # Individual experiment logs
│   └── 001-architect/    # First experiment: can the architect build a product?
└── findings/             # Friction points, bugs, design issues found
```
