# Data Macro Analysis

## Data Asset Inventory

```text
Structured data:
- .claude-plugin/plugin.json
- hooks/hooks.json
- sample_input.json / expected_output.json (in many skills)
- settings and local config under .claude/

Semi-structured data:
- Markdown contracts (agents, commands, skills)
- README and docs

Generated/derived artifacts:
- reports and generated docs in skill-local folders
- caches under __pycache__ and local runtime folders
```

## Logical Entities and Relationships

```text
Plugin
  -> defines package metadata and distribution identity

Command
  -> triggers workflows
  -> may call Agents and Skills

Agent
  -> defines role, tools, and behavior contract
  -> may produce or update artifacts

Skill
  -> defines reusable capability and scripts
  -> may consume sample_input and produce reports

Hook
  -> intercepts lifecycle events and tool execution
```

## Relationship Diagram (ASCII)

```text
+---------+      +----------+      +--------+
| Command | ---> |  Agent   | ---> | Skill  |
+---------+      +----------+      +--------+
      |                |                |
      +----------------+----------------+
                       |
                       v
                 +-----------+
                 | Artifacts |
                 | docs/json |
                 +-----------+
                       ^
                       |
                   +-------+
                   | Hooks |
                   +-------+
```

## Migration/Version History Sources

- Version source: .claude-plugin/plugin.json
- Release and behavior history: README.md and skill/agent changelogs where present
- Git history is the authoritative migration timeline

## Data Lifecycle and Ownership

- Authoring: markdown contracts and scripts by maintainers
- Execution: Claude Code + toolchain + hooks
- Persistence: repository files committed to git
- Consumption: contributors, users, and automation agents

## Integrity Risks

- Contract files can drift without schema validation.
- Sample input/output fixtures may become stale.
- Local cache or generated artifacts can be mistaken for source of truth.
