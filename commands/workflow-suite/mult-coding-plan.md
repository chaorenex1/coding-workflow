---
description: Generate a multi-backend implementation plan via mult-analysis-planner, then optionally persist it via plan-write
argument-hint: [feature-or-task-description]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, TaskOutput, Skill, AskUserQuestion
---

# Multi Coding Plan

Create a multi-backend implementation plan and optionally save it as a plan file.

## Workflow

1. Use `$ARGUMENTS` as the planning requirement.
2. Invoke agent: `mult-analysis-planner`.
3. Present generated plan summary to user.
4. Ask user: `是否保存成计划文件？(yes/no)`.
5. If user says `yes`:
   - Invoke agent: `plan-write`
   - Save/version the plan under `.claude/plan/`
6. If user says `no`:
   - Return plan directly in chat and stop.

## Output

- Planning summary
- Save decision result
- Plan file path (if saved)

## Integration with Other Commands

After planning:
- Use `/mult-tdd-coder` to implement with test-driven development across multiple backends

## Related Agents

This command invokes the `mult-analysis-planner` and `plan-write` agents

