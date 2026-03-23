---
description: Generate a standard implementation plan via analysis-planner, then optionally persist it via plan-write
argument-hint: [feature-or-task-description]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, TaskOutput, Skill, AskUserQuestion
---

# Coding Plan

Create a standard implementation plan and optionally save it as a plan file.

## Workflow

1. Use `$ARGUMENTS` as planning input.
2. Invoke agent: `analysis-planner`.
3. Present generated plan summary to user.
4. Ask user: `是否保存成计划文件？(yes/no)`.
5. If user says `yes`:
   - Invoke agent: `plan-write`
   - Save/version under `.claude/plan/`
6. If user says `no`:
   - Return plan directly in chat and stop.

## Output

- Planning summary
- Save decision result
- Plan file path (if saved)

## Integration with Other Commands

After planning:
- Use `/tdd-coder` to implement with test-driven development

## Related Agents

This command invokes the `analysis-planner` and `plan-write` agents