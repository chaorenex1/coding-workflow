---
description: Execute the quality-reviewer agent for code quality, security, and maintainability review
argument-hint: [optional-scope-or-change-summary]
allowed-tools: Read, Grep, Glob, Bash, Task, TaskOutput, Skill
---

# Quality Review

Run the `quality-reviewer` agent to review current changes for code quality, security, and maintainability.

## Workflow

1. Use `$ARGUMENTS` as optional review scope, context, or change summary.
2. Invoke agent: `quality-reviewer`.
3. Review current staged and unstaged changes, or recent commits when no diff is available.
4. Return the `quality-reviewer` agent output.

## Notes

- Use this command after implementation, refactoring, or other code changes that require the mandatory quality review pass.
- Findings should prioritize bugs, regressions, security concerns, and maintainability risks over style-only comments.
