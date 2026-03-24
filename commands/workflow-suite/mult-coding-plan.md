---
description: Generate a multi-backend implementation plan via mult-analysis-planner, then optionally persist it via plan-write
argument-hint: [feature-or-task-description]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, TaskOutput, Skill, AskUserQuestion
---

# Multi Coding Plan

Create a multi-backend implementation plan and optionally save it as a plan file.

## What This Command Does

1. **Restate Requirements** - Clarify what needs to be built
2. **Identify Risks** - Surface potential issues and blockers
3. **Create Step Plan** - Break down implementation into phases
4. **Wait for Confirmation** - MUST receive user approval before proceeding
5. **Persist Plan** - Save the plan file if approved by the user

## When to Use

Use `/mult-coding-plan` when:
- The task spans backend and frontend and needs coordinated planning
- You want a clear step-by-step plan before implementation
- You want versioned plan persistence for later execution by `/mult-tdd-coder`

## How It Works

The mult-analysis-planner and plan-write agents will:

1. **Analyze the request** and restate requirements in clear terms
2. **Break down into phases** with specific, actionable steps
3. **Identify dependencies** between components
4. **Assess risks** and potential blockers
5. **Estimate complexity** (High/Medium/Low)
6. **Present the plan** and WAIT for your explicit confirmation
7. **Persist the plan** if approved by the user

## Example Usage

### Input

`/mult-coding-plan implement cross-backend command telemetry with dashboard visibility`

### Example Output

```markdown
${the `mult-analysis-planner` agent output here}

## Confirmation
**WAITING FOR CONFIRMATION**: Proceed with this plan? (yes/no/modify)

## Save Decision
- User choice: yes
- Plan saved: .claude/plan/cross-backend-command-telemetry.md
```

## Integration with Other Commands

After planning:
- Use `/mult-tdd-coder` to implement with test-driven development across multiple backends

## Related Agents

This command invokes the `mult-analysis-planner` and `plan-write` agents

