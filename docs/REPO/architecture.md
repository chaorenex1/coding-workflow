# Repository Architecture

## System Context

This repository is a Claude Code plugin project that packages:
- Agents (task-specialized personas)
- Commands (slash-command workflows)
- Skills (capability modules)
- Hooks (session and tool guardrails)
- Prompts and documentation

The runtime center is plugin metadata and Claude Code auto-discovery conventions.

## High-Level System Diagram (ASCII)

```text
+---------------------+      +---------------------------+
| User in Claude Code | ---> | Slash Commands / Agents   |
+---------------------+      +---------------------------+
             |                            |
             |                            v
             |                  +-------------------------+
             |                  | Skills (domain modules) |
             |                  +-------------------------+
             |                            |
             v                            v
+---------------------+      +---------------------------+
| Hook Layer          | ---> | Tool Execution Layer      |
| SessionStart        |      | Read/Write/Grep/Glob/Bash|
| PreToolUse          |      +---------------------------+
+---------------------+                    |
                                           v
                              +-----------------------------+
                              | Repo Artifacts + Docs       |
                              | agents/ commands/ skills/   |
                              | docs/ prompts/ hooks/       |
                              +-----------------------------+
```

## Service Boundaries

- Orchestration boundary
  - commands/
  - agents/
  - prompts/
- Capability boundary
  - skills/
- Policy and guard boundary
  - hooks/
  - CLAUDE.md
- Distribution boundary
  - .claude-plugin/plugin.json
- Documentation boundary
  - docs/
  - README.md

## End-to-End Data Flow

```text
User request
  -> command or agent prompt selection
  -> relevant skill/tool chain execution
  -> hook validation (SessionStart/PreToolUse)
  -> generated outputs (code/docs/reports)
  -> repository files updated
```

## Architectural Notes

- The architecture is plugin-first, not app-server-first.
- Most behavior is convention-driven by directory layout and markdown frontmatter.
- Runtime and governance are mostly encoded in markdown contracts and Python hook scripts.

## Assumptions and Open Questions

- Assumption: Claude Code auto-discovers skills/agents/commands based on folder conventions.
- Assumption: memex-cli is an optional backend accelerator, not a hard runtime dependency for every task.
- Open question: Is there a canonical compatibility matrix for command-to-agent versioning?
