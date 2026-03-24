---
description: Generate a standard implementation plan via analysis-planner, then optionally persist it via plan-write
argument-hint: [feature-or-task-description]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, TaskOutput, Skill, AskUserQuestion
---

# Coding Plan

Create a standard implementation plan and optionally save it as a plan file.

## What This Command Does

1. **Restate Requirements** - Clarify what needs to be built
2. **Identify Risks** - Surface potential issues and blockers
3. **Create Step Plan** - Break down implementation into phases
4. **Wait for Confirmation** - MUST receive user approval before proceeding
5. **Persist Plan** - Save the plan file if approved by the user

## When to Use

Use `/coding-plan` when:
- Starting a new feature
- Making significant architectural changes
- Working on complex refactoring
- Multiple files/components will be affected
- Requirements are unclear or ambiguous

## How It Works

The analysis-planner and plan-write agents will:

1. **Analyze the request** and restate requirements in clear terms
2. **Break down into phases** with specific, actionable steps
3. **Identify dependencies** between components
4. **Assess risks** and potential blockers
5. **Estimate complexity** (High/Medium/Low)
6. **Present the plan** and WAIT for your explicit confirmation
7. **Persist the plan** if approved by the user

## Example Usage

### Input

`/coding-plan add command-level telemetry summary for workflow-suite`

### Example Output

```markdown
${The analysis-planner output here}

## Confirmation
**WAITING FOR CONFIRMATION**: Proceed with this plan? (yes/no/modify)

## Save Decision
- User choice: yes
- Plan saved: .claude/plan/command-level-telemetry-summary.md
```

## Integration with Other Commands

After planning:
- Use `/tdd-coder` to implement with test-driven development

## Related Agents

This command invokes the `analysis-planner` and `plan-write` agents