# Brief: MCP notifications/tools/list_changed after config_reload

## Problem

After calling `config_reload` via MCP, new workflow tools are registered in the engine but MCP clients (e.g., Claude Code) don't see them. The client's tool list is cached at connection time. The MCP spec provides `notifications/tools/list_changed` for exactly this — but konf-mcp doesn't send it.

**Finding reference:** `konf-experiments/findings/003-mcp-hot-reload-invisible.md`

**Current workaround:** Restart the MCP client to see new tools.

## Location

Primary files to modify:

- `crates/konf-mcp/src/lib.rs` — KonfMcpServer (lines 35-67), serve_stdio (lines 48-56), ServerHandler impl (lines 97-211)
- `crates/konf-mcp/src/main.rs` — binary entrypoint (lines 12-49)
- `crates/konf-init/src/lib.rs` — ConfigReloadTool (lines 310-399)

Key dependencies to understand:

- `rmcp 1.3.0` — the MCP SDK. `Peer<RoleServer>` has `notify_tool_list_changed()` method. `RunningService` has `peer()` method that returns the Peer.
- `konflux-core` Engine — `register_tool()`, `remove_tool()`, `registry()` methods

## Root Cause

1. `serve_stdio()` calls `self.serve(transport).await` which returns a `RunningService`
2. `RunningService` has a `peer()` method that gives access to the `Peer<RoleServer>`
3. `Peer<RoleServer>` has `notify_tool_list_changed()` — this is what we need to call
4. But currently, `serve_stdio()` just calls `service.waiting().await` and blocks
5. `ConfigReloadTool` modifies the engine's tool registry but has no way to signal the MCP server that tools changed

The fix requires connecting these two: when ConfigReloadTool finishes, the MCP server's Peer must be notified.

## Approach

The simplest approach that maintains separation of concerns:

1. Add a `tokio::sync::watch` channel to `KonfMcpServer` (or pass it through the engine)
2. After `serve()` returns the `RunningService`, spawn a background task that:
   - Watches for tool change signals on the channel
   - Calls `service.peer().notify_tool_list_changed().await` when signaled
3. ConfigReloadTool sends on the channel after successful reload
4. The channel is stored in a shared location accessible to both (e.g., as a field on the Engine, or passed through KonfInstance)

Alternative simpler approach: store the tool registry length/hash before each `list_tools` call and compare. If changed, send the notification inline. But this only works if the client re-requests the tool list, which it won't without the notification.

## Success Criteria

- [ ] `cd konf && cargo test --workspace` — all tests pass
- [ ] `cd konf && cargo clippy --workspace -- -D warnings` — no warnings
- [ ] After `config_reload` adds a new workflow tool, the MCP server sends `notifications/tools/list_changed` to connected clients
- [ ] The notification mechanism uses a clean channel pattern (not polling or sleeping)
- [ ] No changes to files outside of `crates/konf-mcp/` and `crates/konf-init/` (and minimally `crates/konflux-core/` if a channel needs to be stored on Engine)

## Constraints

- Only modify the files listed in Location
- Follow Rust rules: no `.unwrap()` or `.expect()` in production code, propagate errors with `?`
- Use `tokio::sync::watch` or `tokio::sync::broadcast` — don't add new dependencies
- The MCP server must remain a thin shell — no business logic
- ConfigReloadTool must not directly depend on konf-mcp types (separation of concerns)
- Keep the change minimal — this is a notification, not an architecture rewrite

## Context

Read these files first for background:

- `bootstrap/CLAUDE.md` — agent instructions (local only, gitignored)
- `crates/konf-mcp/src/lib.rs` — full file, understand current structure
- `crates/konf-init/src/lib.rs` — ConfigReloadTool implementation
- `crates/konflux-core/src/engine.rs` — Engine struct and tool registry
- `konf-experiments/findings/003-mcp-hot-reload-invisible.md` — the original finding
