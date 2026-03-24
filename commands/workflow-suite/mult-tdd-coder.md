---
description: Execute the mult-tdd-coder agent for multi-backend test-first implementation orchestration
argument-hint: [approved-plan-task-or-implementation-description]
allowed-tools: Read, Bash, Glob, Grep, Task, TaskOutput, Skill, AskUserQuestion
---

# Multi TDD Coder

Run the `mult-tdd-coder` agent to orchestrate strict TDD-first implementation across multiple backends.

## What This Command Does

- Runs `mult-tdd-coder` to orchestrate strict RED -> GREEN -> REFACTOR execution across multiple backends.
- Decomposes approved plan tasks into backend, UI, and integration execution units.
- Returns traceable delivery evidence including validation, coverage, and RUN_ID lineage.

## When to Use

Use `/mult-tdd-coder` when:
- You already have an approved plan and need coordinated multi-backend execution
- The work spans backend logic and UI behavior under one TDD contract
- You need strict gate control and session lineage traceability

## How It Works

The mult-tdd-coder agent will:

1. **Parse approved input** from `$ARGUMENTS`
2. **Build execution scope** with plan references and required test levels
3. **Decompose into task waves** for backend/UI/integration ownership
4. **Enforce TDD gates** in strict order: RED -> GREEN -> REFACTOR
5. **Return orchestration evidence** including coverage status and RUN_ID lineage

## TDD Cycle

```
RED → GREEN → REFACTOR → REPEAT

RED:      Write a failing test
GREEN:    Write minimal code to pass
REFACTOR: Improve code, keep tests passing
REPEAT:   Next feature/scenario
```

## Example Usage

### Input

`/mult-tdd-coder execute plan task 2.1-2.4 for telemetry pipeline and dashboard states`

### Example Output

```markdown
## TDD Execution Scope
- Task Type: Fullstack
- Source: saved plan
- Plan References: [2.1, 2.2, 2.3, 2.4]
- Primary Areas: [skills/memex-cli, skills/component-tester]
- Required Tests: unit | integration
- Coverage Target: >= 80%

## TDD Task Breakdown
| ID | Plan Ref | Type | Description | File Scope | Dependencies | Gate 1: RED (must fail) | Gate 2: GREEN (must pass) | Gate 3: REFACTOR (keep green) |
|----|----------|------|-------------|------------|--------------|---------------------------|----------------------------|-------------------------------|
| task-1 | 2.1 | code-test | telemetry ingestion tests + minimal parser | skills/memex-cli/** | None | pytest -k telemetry_ingest | pytest -k telemetry_ingest | pytest -k telemetry && ruff check |
| task-2 | 2.3 | ui-test | dashboard state tests + minimal UI state wiring | skills/component-tester/** | task-1 | npm test -- dashboard_state | npm test -- dashboard_state | npm run lint && npm test |

## Validation and Lineage
- RED/GREEN/REFACTOR gates completed for listed tasks.
- Coverage Status: 84% (meets >= 80% target)
- CODEX_SESSION: run_codex_7f21
- GEMINI_SESSION: run_gemini_91ac
- Remaining Blockers: None
```

## Notes

- Use this command when implementation must be coordinated across Codex, Gemini, and Claude under a single TDD contract.
- Prefer an approved plan from `analysis-planner` or `mult-analysis-planner` before execution.
