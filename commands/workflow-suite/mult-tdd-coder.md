---
description: Execute the mult-tdd-coder agent for multi-backend test-first implementation orchestration
argument-hint: [approved-plan-task-or-implementation-description]
allowed-tools: Read, Bash, Glob, Grep, Task, TaskOutput, Skill, AskUserQuestion
---

# Multi TDD Coder

Run the `mult-tdd-coder` agent to orchestrate strict TDD-first implementation across multiple backends.

## Workflow

1. Use `$ARGUMENTS` as the approved plan task or implementation requirement.
2. Invoke agent: `mult-tdd-coder`.
3. Let the agent decompose work into backend, UI, and integration execution units when needed.
4. Enforce RED -> GREEN -> REFACTOR gates for each execution unit.
5. Return a concise orchestration summary including:
   - task breakdown or plan references
   - files changed
   - test and validation evidence
   - coverage status
   - RUN_ID or session lineage when available
   - remaining blockers or approval items

## Notes

- Use this command when implementation must be coordinated across Codex, Gemini, and Claude under a single TDD contract.
- Prefer an approved plan from `analysis-planner` or `mult-analysis-planner` before execution.
