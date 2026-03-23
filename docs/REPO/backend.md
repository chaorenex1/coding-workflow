# Backend Macro Analysis

## What "Backend" Means in This Repository

This project does not expose a traditional HTTP backend service. Its backend behavior is orchestration logic distributed across:
- commands/*.md
- agents/*.md
- skills/*
- hooks/*.py

## Execution Entry Points

- Slash commands under commands/
- Agent profiles under agents/
- Session and tool controls under hooks/hooks.json

## Route Map (Conceptual)

```text
User Intent
  -> Command (commands/*) OR Agent (agents/*)
  -> Skill selection (skills/*/SKILL.md)
  -> Tool calls (Read/Write/Grep/Glob/Bash/...)
  -> Output persisted to repository files
```

## Middleware Chain (Hook Chain)

```text
Session Start
  -> hooks/check-deps.py
      - verifies required dependencies

Before Tool Use
  -> hooks/pre-tool-use.py
      - policy/guard validation before tool execution
```

## Service -> Repository Mapping

```text
Service Layer (logical)         Repository Area
------------------------------  ---------------------------------
Workflow orchestration          commands/, agents/
Capability implementation       skills/
Policy enforcement             hooks/, CLAUDE.md
Distribution metadata          .claude-plugin/plugin.json
Reference documentation        README.md, docs/
Prompt contracts               prompts/
```

## API-Like Surfaces

- Command names under commands/ behave like user-facing API routes.
- Agent names under agents/ behave like internal service endpoints.
- Skill names under skills/ behave like pluggable capability modules.

## Key Risks

- Contract drift between command instructions and agent instructions.
- Tool permission assumptions diverging across markdown specs.
- Hook logic and markdown policies becoming inconsistent over time.
