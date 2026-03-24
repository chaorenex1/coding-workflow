---
description: Generate a multi-backend implementation plan via mult-analysis-planner, then optionally persist it via plan-write
argument-hint: [feature-or-task-description]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, TaskOutput, Skill, AskUserQuestion
---

# Multi Coding Plan

Create a multi-backend implementation plan and optionally save it as a plan file.

## What This Command Does

- Runs `mult-analysis-planner` to produce a multi-backend implementation plan.
- Presents a structured plan summary with execution-ready phases.
- Optionally persists the plan via `plan-write` under `.claude/plan/`.

## When to Use

Use `/mult-coding-plan` when:
- The task spans backend and frontend and needs coordinated planning
- You want a clear step-by-step plan before implementation
- You want versioned plan persistence for later execution by `/mult-tdd-coder`

## How It Works

The mult-analysis-planner and plan-write agents will:

1. **Parse planning requirement** from `$ARGUMENTS`
2. **Generate multi-backend plan** with backend/frontend perspectives
3. **Present plan** for user confirmation
4. **Ask save decision**: `是否保存成计划文件？(yes/no)`
5. **If yes, persist plan** via `plan-write` into `.claude/plan/`
6. **If no, return plan directly** in chat and stop

## Output

- Plan details
- Save decision result
- Plan file path (if saved)

## Example Usage

### Input

`/mult-coding-plan implement cross-backend command telemetry with dashboard visibility`

### Example Output

```markdown
# Implementation Plan: Cross-Backend Command Telemetry

## Overview
Introduce telemetry collection in command execution and expose dashboard-friendly summaries.

## Task Type
- [x] Fullstack (parallel backend/frontend analysis)

## Implementation Steps
### Phase 1: Backend Event Instrumentation
1. **Capture command execution events** (File: skills/memex-cli/bridge.py)

### Phase 2: Frontend Contract and Visibility
1. **Define dashboard query contract** (File: skills/component-tester/scripts/dashboard_state.ts)

## Technical Solution
Use backend event instrumentation with contract-tested schema mapping, then expose a stable UI query contract for dashboard aggregation.

## Key Files
| File | Operation | Description |
|------|-----------|-------------|
| skills/memex-cli/bridge.py | Modify | Emit and normalize telemetry events |
| skills/component-tester/scripts/dashboard_state.ts | Modify | Consume telemetry summary contract |

## Testing Strategy
- Unit tests: telemetry event normalization and schema guards
- Integration tests: end-to-end command event flow to storage/query boundary
- E2E tests: dashboard state rendering for success/empty/error cases

## Risks and Mitigation
| Risk | Mitigation |
|------|------------|
| Schema drift | Versioned event schema and contract tests |

## Success Criteria
- [ ] Telemetry events emitted for all target command paths
- [ ] Dashboard can display aggregated telemetry states reliably
- [ ] Contract tests and regression checks pass

## RUN_ID
- CODEX_SESSION: run_codex_abc123
- GEMINI_SESSION: run_gemini_def456

## Save Decision
- User choice: yes
- Plan saved: .claude/plan/cross-backend-command-telemetry.md
```

## Integration with Other Commands

After planning:
- Use `/mult-tdd-coder` to implement with test-driven development across multiple backends

## Related Agents

This command invokes the `mult-analysis-planner` and `plan-write` agents

