# Brief: [title]

## Problem

What's broken or missing. Include error messages, finding references, or user-facing symptoms.

## Location

Exact file paths and line numbers. What the agent should read first.

## Root Cause

What we've already investigated. Saves the agent from re-discovering known information.

## Success Criteria

Each criterion must be independently verifiable by running a command.

- [ ] `cd konf && cargo test --workspace` — all tests pass
- [ ] `cd konf && cargo clippy --workspace -- -D warnings` — no warnings
- [ ] [specific behavioral test — describe exact command and expected output]

## Constraints

- Only modify files listed in Location (no unrelated changes)
- Follow Rust rules: no `.unwrap()` or `.expect()` in production code, propagate errors with `?`
- Follow konf design principles: Rust = mechanisms, workflows = policies
- Do not add new dependencies without justification

## Context

Files to read for background understanding:

- `CLAUDE.md` — project philosophy and workspace rules
- [relevant architecture docs]
- [relevant finding doc]
