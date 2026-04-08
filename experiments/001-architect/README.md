# Experiment 001: The Architect

**Goal:** Connect an MCP client to Konf and build a product through conversation.

**Hypothesis:** The four new tools (shell:exec, yaml:validate_workflow, system:introspect, config:reload) are sufficient for an AI to create, validate, and deploy Konf workflows without writing Rust code.

## Setup

1. Build Konf with the architect config
2. Start the sandbox container
3. Connect Claude Code via MCP
4. Ask it to build a simple workflow (e.g., weather fetcher)

## What to Observe

- Does system:introspect give the AI enough context to write valid YAML?
- Does yaml:validate_workflow produce errors the AI can fix?
- Does the full loop work: generate → validate → write → reload → execute?
- Does git checkpointing work via shell:exec?
- What friction points appear?

## Log

_(Fill in as we run the experiment)_

## Findings

_(Friction points, bugs, and design issues — copy to ../findings/)_
