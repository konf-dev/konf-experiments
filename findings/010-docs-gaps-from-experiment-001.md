# Finding 010: Documentation gaps found during Experiment 001

**Experiment:** 001
**Date:** 2026-04-09
**Severity:** Medium — causes confusion for anyone using Konf

## YAML Schema Documentation

### `with:` vs `input:` confusion
- `docs/product-guide/workflow-reference.md` documents both `with:` (static) and `input:` (dynamic/templated) as valid node fields
- In reality, only `with:` exists in the parser schema (`parser/schema.rs`)
- `input:` is silently ignored by serde — no error, just empty tool input
- **Fix:** Remove `input:` from docs or implement it. Clarify that `with:` handles both static values and `{{template}}` expressions.

### Template reference prefix
- Docs show `{{message}}` as a top-level reference to workflow input
- Actually, workflow input is stored under the `input` key — must use `{{input.message}}`
- Node outputs use `{{node_name.field}}` — this is correct in docs
- **Fix:** Update all examples to use `{{input.fieldname}}` for workflow arguments

### `register_as_tool` requirement
- Not emphasized that workflows need `register_as_tool: true` AND non-empty `capabilities` to be callable
- A workflow with `capabilities: []` fails at runtime with capability denied errors
- **Fix:** Document that `capabilities: ["*"]` or specific grants are required

### Single entry node
- The workflow-reference.md describes "start nodes" as nodes with no incoming edges
- Actually, the parser enforces a single entry node (first in YAML order)
- All other nodes must be reachable from it via `then:` edges
- **Fix:** Document the single-entry-node constraint and the fan-out pattern for parallel execution

## MCP Integration Documentation

### .mcp.json vs settings.json
- No docs explain where to put MCP server configuration for Claude Code
- We discovered it must go in `.mcp.json`, not `.claude/settings.json`
- **Fix:** Add a "Connecting via MCP" guide with exact `.mcp.json` format

### Tool name restrictions
- No docs mention that tool names with colons break Claude Code
- We renamed everything from `prefix:name` to `prefix_name`
- **Fix:** Document the underscore naming convention and why

### MCP annotations incompatibility
- No docs mention that MCP 2025-11-25 annotations cause Claude Code to drop all tools
- **Fix:** Add a "Known MCP Client Compatibility" section

### Hot-registered tools invisible
- No docs explain that `config_reload` registers new tools but MCP clients won't see them until reconnection
- **Fix:** Document this limitation and the workaround (restart client)

## Configuration Documentation

### tools.yaml env var interpolation
- `docs/admin-guide/security.md` claims `tools.yaml` supports `${VAR:-default}` interpolation
- It doesn't — only `konf.toml` supports env vars via figment
- **Fix:** Correct the docs. Either implement interpolation or remove the claim.

### KONF_DEV_MODE
- Not documented anywhere that `KONF_DEV_MODE=true` bypasses JWT auth
- Useful for development but should be in the quickstart guide
- **Fix:** Add to admin-guide/deployment.md

### Config directory paths
- `KONF_CONFIG_DIR` env var is the primary way to specify config location
- Not clearly documented — had to find it in the code
- **Fix:** Add to quickstart and admin-guide

## Architecture Documentation

### Workflow execution model
- `docs/architecture/engine.md` describes the DAG executor but doesn't explain:
  - How workflow input is stored (`state.set_output("input", input)`)
  - How template resolution works (`template::resolve_inputs`)
  - The difference between `with` (the only real field) and `input` (documented but nonexistent)
- **Fix:** Add a "Template Resolution" section explaining the state model

### WorkflowTool child scope
- `docs/architecture/runtime.md` describes capability attenuation for child scopes
- Does not explain that WorkflowTool creates a NEW engine with copied tools
- Does not explain that workflow tools (`workflow_*`) may not be available in child scopes
- **Fix:** Document the tool copying mechanism and its current limitation (Finding 009)

### MCP server capabilities
- `docs/architecture/mcp.md` describes konf-mcp but doesn't mention:
  - MCP calls get `capabilities: ["*"]` (infra-level access)
  - No namespace injection on MCP calls
  - No `notifications/tools/list_changed` support
- **Fix:** Add a "Current Limitations" section

## Missing Guides

### "Building Your First Workflow via MCP" tutorial
- The exact steps we followed in Experiment 001 should be a guide:
  1. Start sandbox container
  2. Configure `.mcp.json`
  3. Call `system_introspect`
  4. Write, validate, deploy a workflow
  5. Test it
- This is the most valuable doc for anyone trying Konf

### "Workflow YAML Cookbook"
- Patterns we tested: single tool, sequential, parallel, error handling, LLM composition
- Each pattern with working YAML and expected output
- This is pure gold for product builders
