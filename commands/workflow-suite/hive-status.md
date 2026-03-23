---
description: Show current Hive orchestration status from .hive state and report artifacts
argument-hint: [optional-task-id-or-objective-keyword]
allowed-tools: Read, Glob, Grep, Bash
---

# Hive Status

Inspect Hive runtime artifacts and return the latest orchestration status.

## Usage

`/hive-status [optional-task-id-or-objective-keyword]`

## Workflow

1. Read `.hive/state.yaml` as the primary status source.
2. If present, read `.hive/summary.md` for latest executive summary.
3. Discover available team reports under `.hive/reports/`.
4. If `$ARGUMENTS` is provided, filter status/reports by task id or objective keyword.
5. Return a concise status board including objective, current wave, overall status, orchestration mode, active lanes, ready queue, per-team progress, dependency health, open decisions, blockers, and last updated artifacts.

## Output Contract

```markdown
## Hive Status

- Objective: <text>
- Current Wave: <wave-id>
- Overall Status: <pending|in_progress|blocked|complete>
- Orchestration Mode: <parallel-wave-dag>
- Active Lanes: <list>
- Ready Queue: <list>
- Team Progress:
  - strategy: <status>
  - analysis: <status>
  - architecture: <status>
  - implementation: <status>
  - quality: <status>
  - docs: <status>
  - platform: <status>
- Dependency Health: <ok|degraded|blocked>
- Open Decisions: <none|list>
- Blockers: <none|list>
- Recent Artifacts:
  - .hive/state.yaml
  - .hive/summary.md
  - .hive/reports/<team>/...
```

## Notes

- This command is read-only and should not modify `.hive` state.
- If no Hive state exists, return a clear message and suggest running `/hive` first.
