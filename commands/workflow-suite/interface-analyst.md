---
description: Run interface-level and function-level micro analysis documentation via interface-analyst
argument-hint: [scope|path|depth]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, TaskOutput, Skill
---

# Interface Analyst

Run the `interface-analyst` agent for micro-level interface/function contract analysis.

## Workflow

1. Parse `$ARGUMENTS` as optional target scope (path/depth/include-internal hints).
2. Invoke agent: `interface-analyst`.
3. Generate or refresh micro docs under `./.claude/docs/micro/` (or agent-selected output path).
4. Return:
   - analyzed scope
   - generated/updated files
   - top contract risks and improvement items

## Notes

- Focus on interfaces, functions, call chains, and integration contracts.
- Prefer concrete evidence and file references in output.
