---
description: Execute the tdd-coder agent for strict test-first implementation
argument-hint: [feature-or-bugfix-description]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, TaskOutput, Skill
---

# TDD Coder

Run the `tdd-coder` agent to implement work through a strict RED -> GREEN -> REFACTOR cycle.

## What This Command Does

- Runs the `tdd-coder` agent for strict test-first delivery.
- Enforces RED -> GREEN -> REFACTOR execution gates.
- Returns delivery evidence with changed files, test result, and coverage status.

## When to Use

Use `/tdd-coder` when:
- Implementing new features
- Adding new functions/components
- Fixing bugs (write test that reproduces bug first)
- Refactoring existing code
- Building critical business logic

## How It Works

The tdd-guide agent will:

1. **Define interfaces** for inputs/outputs
2. **Write tests that will FAIL** (because code doesn't exist yet)
3. **Run tests** and verify they fail for the right reason
4. **Write minimal implementation** to make tests pass
5. **Run tests** and verify they pass
6. **Refactor** code while keeping tests green
7. **Check coverage** and add more tests if below 80%

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

`/tdd-coder add retry policy for memex-cli execution with test-first validation`

### Example Output

```markdown
## Delivery Summary

### Files Changed
- skills/codex-cli-bridge/codex_executor.py
- skills/codex-cli-bridge/test_skill.py

### TDD Evidence
- RED: Added failing test for retry-on-timeout path and verified failure.
- GREEN: Implemented minimal retry logic to satisfy failing test.
- REFACTOR: Simplified retry helper and kept behavior unchanged.

### Test Status
- Unit tests: PASS
- Integration tests: PASS

### Coverage Status
- Overall coverage: 83% (meets >= 80% target)

### Remaining Risks
- Backoff policy tuning for very slow networks may require follow-up load tests.
```

## Notes

- Use this command for feature work, bug fixes, or refactors that should be delivered with strict TDD.
- Coverage target follows the agent guidance: 80%+ unless the project requires a stricter threshold.
