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

This command must follow a two-stage workflow:

1. **Run `mult-analysis-planner` only** to analyze the request and generate the draft plan
2. **Show the full planning output in chat** without saving anything
3. **Ask for confirmation** using `yes`, `no`, or `modify`
4. **If the user says `yes`**, invoke `plan-write` to persist the approved plan
5. **If the user says `modify`**, collect changes, rerun `mult-analysis-planner`, and ask again
6. **If the user says `no`**, stop and do not write any plan file

`plan-write` is a persistence-only step and must not generate or fetch a replacement plan on its own.

## Workflow

1. Use `$ARGUMENTS` as the planning request.
2. Invoke agent: `mult-analysis-planner`.
3. Return the full `mult-analysis-planner` output, ending with the confirmation prompt that asks the user to approve, discard, or revise the plan.
4. Use `AskUserQuestion` to capture `yes`, `no`, or `modify`.
5. Only when the answer is `yes`, invoke `plan-write` with the approved plan content.
6. If the answer is `modify`, revise the request and repeat from step 2.
7. If the answer is `no`, stop without persisting anything.

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
