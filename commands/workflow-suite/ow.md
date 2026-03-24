---
description: Sequential and tmux/worktree orchestration guidance for multi-agent workflows.
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, TaskOutput, Skill, AskUserQuestion
---

# Orchestrate Command

Sequential agent workflow for complex tasks.

## Usage

`/ow [workflow-type] [task-description]`

## Workflow Types

### feature
Full feature implementation workflow:
```
analysis-planner -> tdd-coder -> quality-reviewer -> security-checker
```

### bugfix
Bug investigation and fix workflow:
```
analysis-planner -> tdd-coder -> quality-reviewer
```

### refactor
Safe refactoring workflow:
```
architect-designer -> quality-reviewer -> tdd-coder
```

### security
Security-focused review:
```
security-checker -> quality-reviewer -> architect-designer
```

### cleanup
Multi-language dead code cleanup workflow:
```
dead-code-cleaner -> quality-reviewer -> quality-reviewer
```

### review
Comprehensive quality and security review (read-only, no code changes):
```
quality-reviewer -> security-checker
```

### docs
Documentation synchronization after code changes:
```
documentation-sync-agent -> quality-reviewer
```

### multcode
Multi-backend collaborative feature using Codex and Gemini:
```
mult-analysis-planner -> mult-tdd-coder -> quality-reviewer -> security-checker
```

### architecture
Architecture design followed by plan persistence:
```
architect-designer -> analysis-planner (waits for approval) -> user confirmation -> plan-write
```

### analysis
Macro and micro codebase analysis with architecture assessment:
```
repo-analyst -> interface-analyst -> architect-designer
```
Outputs: `docs/REPO/` + `./.claude/docs/micro/`

### repo-analysis
Repository-level macro documentation generation:
```
repo-analyst
```
Outputs: `docs/REPO/architecture.md`, `backend.md`, `frontend.md`, `data.md`, `dependencies.md`

### interface-analysis
Interface and function contract analysis:
```
interface-analyst
```
Outputs: `./.claude/docs/micro/` scoped analysis files

### repo-sync
Refresh existing repository analysis documents in place:
```
repo-analyst
```
Behavior: equivalent to `repo-analyst-sync`, focused on updating existing `docs/REPO/` files instead of creating parallel copies

### plan
Standard implementation planning with user-gated persistence:
```
analysis-planner -> WAIT for user (yes/no/modify) -> [if yes] plan-write
```
Outputs: planning summary in chat, saved under `.claude/plan/` only after explicit approval

### mult-plan
Multi-backend implementation planning with user-gated persistence:
```
mult-analysis-planner -> WAIT for user (yes/no/modify) -> [if yes] plan-write
```
Outputs: multi-backend planning summary in chat, saved under `.claude/plan/` only after explicit approval

## Execution Pattern

For each agent in the workflow:

1. **Invoke agent** with context from previous agent
2. **Collect output** as structured handoff document
3. **Check for required approval gate** in the active workflow
4. **If a gate exists, ask user** with `AskUserQuestion` using `yes/no/modify`
5. **Pass to next agent** only when gate resolves to `yes`
6. **Aggregate results** into final report

## Approval Gate Rules

Use explicit confirmation before running downstream persistence or execution steps:

1. `architecture`: after `analysis-planner` output, require `yes/no/modify` before `plan-write`
2. `plan`: after `analysis-planner` output, require `yes/no/modify` before `plan-write`
3. `mult-plan`: after `mult-analysis-planner` output, require `yes/no/modify` before `plan-write`
4. If user answers `modify`, update request and rerun current planning stage
5. If user answers `no`, stop workflow and return current artifacts without advancing

## Handoff Document Format

Between agents, create handoff document:

```markdown
## HANDOFF: [previous-agent] -> [next-agent]

### Context
[Summary of what was done]

### Findings
[Key discoveries or decisions]

### Files Modified
[List of files touched]

### Open Questions
[Unresolved items for next agent]

### Recommendations
[Suggested next steps]
```

## Example: Feature Workflow

```
/ow feature "Add user authentication"
```

Executes:

1. **Analysis-Planner Agent**
   - Analyzes requirements
   - Creates implementation plan
   - Identifies dependencies
   - Output: `HANDOFF: analysis-planner -> tdd-coder`

2. **TDD Coder Agent**
   - Reads planner handoff
   - Writes tests first
   - Implements to pass tests
   - Output: `HANDOFF: tdd-coder -> quality-reviewer`

3. **Code Reviewer Agent**
   - Reviews implementation
   - Checks for issues
   - Suggests improvements
   - Output: `HANDOFF: quality-reviewer -> security-checker`

4. **Security Checker Agent**
   - Security audit
   - Vulnerability check
   - Final approval
   - Output: Final Report

## Final Report Format

```
ORCHESTRATION REPORT
====================
Workflow: feature
Task: Add user authentication
Agents: analysis-planner -> tdd-coder -> quality-reviewer -> security-checker

SUMMARY
-------
[One paragraph summary]

AGENT OUTPUTS
-------------
Analysis-Planner: [summary]
TDD Coder: [summary]
Code Reviewer: [summary]
Security Checker: [summary]

FILES CHANGED
-------------
[List all files modified]

TEST RESULTS
------------
[Test pass/fail summary]

SECURITY STATUS
---------------
[Security findings]

RECOMMENDATION
--------------
[SHIP / NEEDS WORK / BLOCKED]
```

## Arguments

$ARGUMENTS:
- `feature <description>` - Full feature workflow
- `bugfix <description>` - Bug fix workflow
- `refactor <description>` - Refactoring workflow
- `security <description>` - Security review workflow
- `cleanup <description>` - Multi-language dead code cleanup
- `review <description>` - Comprehensive quality + security review (no changes)
- `docs <description>` - Documentation sync after code changes
- `multcode <description>` - Multi-backend collaborative feature (Codex + Gemini)
- `architecture <description>` - Architecture design and plan persistence
- `analysis <description>` - Macro + micro codebase analysis with architecture assessment
- `repo-analysis <description>` - Generate repository-level docs under docs/REPO
- `interface-analysis <description>` - Generate micro interface/function docs
- `repo-sync <description>` - Refresh existing docs/REPO files in place
- `plan <description>` - Standard implementation planning, optionally saved under .claude/plan
- `mult-plan <description>` - Multi-backend implementation planning, optionally saved under .claude/plan
- `mini-feature <description>` - Small feature quick delivery (≤200 LOC)
- `custom <agents> <description>` - Custom agent sequence

## Custom Workflow Example

```
/ow custom "architect-designer,tdd-coder,quality-reviewer" "Redesign caching layer"
```

## Tips

1. **Start with analysis-planner** for complex features
2. **Always include quality-reviewer** before merge
3. **Use security-checker** for auth/payment/PII
4. **Keep handoffs concise** - focus on what next agent needs
5. **Run verification** between agents if needed
