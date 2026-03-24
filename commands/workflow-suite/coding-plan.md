---
description: Generate a standard implementation plan via analysis-planner, then optionally persist it via plan-write
argument-hint: [feature-or-task-description]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, TaskOutput, Skill, AskUserQuestion
---

# Coding Plan

Create a standard implementation plan and optionally save it as a plan file.

## What This Command Does

- Runs `analysis-planner` to generate a standard implementation plan.
- Presents a structured plan summary for confirmation.
- Optionally persists the plan via `plan-write` under `.claude/plan/`.

## When to Use

Use `/coding-plan` when:
- You need a standard implementation plan before coding
- The task does not require multi-backend planning orchestration
- You want the plan saved and versioned for follow-up execution

## How It Works

The analysis-planner and plan-write agents will:

1. **Parse planning input** from `$ARGUMENTS`
2. **Generate standard plan** through `analysis-planner`
3. **Present plan** to the user
4. **Ask save decision**: `是否保存成计划文件？(yes/no)`
5. **If yes, persist plan** via `plan-write` under `.claude/plan/`
6. **If no, return plan directly** in chat and stop

## Output

- Plan details
- Save decision result
- Plan file path (if saved)

## Example Usage

### Input

`/coding-plan add command-level telemetry summary for workflow-suite`

### Example Output

```markdown
# Implementation Plan: Command-Level Telemetry Summary

## Overview
Add telemetry summary support for workflow-suite command execution. Capture execution events and expose normalization for downstream analytics.

## Requirements
- Telemetry schema for command execution events
- Event capture at command invocation boundaries
- Normalization and contract validation
- Storage/query interface for summary exposure

## Architecture Changes
- commands/workflow-suite/hooks/ — Add execution event hooks
- skills/memex-cli/bridge.py — Telemetry event normalization
- docs/REPO/dependencies.md — Update with telemetry collector reference

## Implementation Steps

### Phase 1: Schema and Hook Definition
1. **Define telemetry event schema** (File: commands/workflow-suite/hooks/telemetry_schema.py)
   - Action: Create event schema with command name, execution time, result status, error context
   - Why: Enforces consistent event structure across all commands
   - Dependencies: None
   - Risk: Low

2. **Add execution hooks** (File: commands/workflow-suite/hooks/hooks.json)
   - Action: Register command execution event hooks
   - Why: Captures execution events at command boundaries
   - Dependencies: Step 1
   - Risk: Low

### Phase 2: Event Capture and Normalization
1. **Implement normalization layer** (File: skills/memex-cli/bridge.py)
   - Action: Add event normalization and validation function
   - Why: Ensures telemetry quality and compatibility
   - Dependencies: Phase 1
   - Risk: Medium (may affect existing bridge execution)

## Testing Strategy
- Unit tests: schema validation and event normalization
- Integration tests: command execution event flow with mocked telemetry collector
- E2E tests: end-to-end telemetry capture for representative command patterns

## Risks & Mitigations
- **Risk**: Schema drift between event producers and consumers
  - Mitigation: Versioned event schema and contract-driven tests
- **Risk**: Incomplete command coverage
  - Mitigation: Audit all existing commands for hook registration

## Success Criteria
- [x] Telemetry event schema defined and documented
- [x] Hooks registered for all target commands
- [x] Event normalization layer passes contract tests
- [x] Integration test coverage >= 80%

## Save Decision
- User choice: yes
- Plan saved: .claude/plan/command-level-telemetry-summary.md
```

## Integration with Other Commands

After planning:
- Use `/tdd-coder` to implement with test-driven development

## Related Agents

This command invokes the `analysis-planner` and `plan-write` agents