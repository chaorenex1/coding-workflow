---
description: Execute the tdd-coder agent for strict test-first implementation
argument-hint: [feature-or-bugfix-description]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, TaskOutput, Skill
---

# TDD Coder

Run the `tdd-coder` agent to implement work through a strict RED -> GREEN -> REFACTOR cycle.

## Workflow

1. Use `$ARGUMENTS` as the implementation requirement.
2. Invoke agent: `tdd-coder`.
3. Enforce test-first execution:
   - write failing tests first
   - implement the minimum change to pass
   - refactor without breaking behavior
4. Return a concise delivery summary including:
   - files changed
   - test status
   - coverage status
   - remaining risks or follow-up items

## Notes

- Use this command for feature work, bug fixes, or refactors that should be delivered with strict TDD.
- Coverage target follows the agent guidance: 80%+ unless the project requires a stricter threshold.
