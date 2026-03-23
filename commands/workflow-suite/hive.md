---
description: Launch the Hive governance workflow with team-based routing, decision gates, and persisted state under .hive/
argument-hint: [objective-or-task-description]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, TaskOutput, Skill, AskUserQuestion
---

# Hive

Run the Hive command as the CEO-facing entry point for cross-team orchestration.

## Usage

`/hive [objective-or-task-description]`

## Workflow

1. Read `$ARGUMENTS` as the current objective and normalize it into one task statement.
2. Ensure required Hive artifacts exist:
   - `.hive/members.yaml`
   - `.hive/templates/team-lead-frontmatter.yaml`
3. Initialize or update `.hive/state.yaml` with:
   - objective
   - inferred task type
   - current wave
   - active tracks
   - team status map
   - dependency graph
   - decisions and blockers
4. Classify task type:
   - `new_feature`
   - `bugfix`
   - `refactor`
   - `review`
   - `docs_only`
   - `deploy`
   - `evaluate`
5. Build a lightweight task DAG in state with nodes, dependencies, and owners:
   - node fields: `id`, `owner`, `depends_on`, `status`, `outputs`
   - status values: `pending`, `ready`, `running`, `blocked`, `done`, `failed`
6. Dispatch by parallel wave instead of fixed stage sequence:
   - Wave 0 (Bootstrap): normalize objective, classify task, hydrate state
   - Wave 1 (Parallel Discovery): run `analysis-planner` and strategy checks in parallel when both are relevant
   - Wave 2 (Parallel Design/Prep): run architecture and implementation prep in parallel when dependency graph allows
   - Wave 3 (Execution): run implementation lanes (`tdd-coder` or `mult-tdd-coder`) with independent subtasks in parallel
   - Wave 4 (Parallel Assurance): run `quality-reviewer`, `security-checker`, and docs sync concurrently after code lanes finish
   - Wave 5 (Consolidation): aggregate reports and produce final CEO summary
7. Apply dynamic triggers to avoid unnecessary linear waiting:
   - trigger docs lane when code or interface contracts changed
   - trigger platform lane only for deploy/build/runtime objectives
   - allow quality pre-checks to start on finished implementation subtasks
8. Enforce decision gates and ask user when required:
   - Plan Gate
   - Design Gate
   - Quality Gate
   - Scope Gate
   - Conflict Gate
9. Persist outputs per wave and lane:
   - `.hive/reports/<team>/...`
   - `.hive/reports/waves/wave-<n>.md`
   - `.hive/summary.md`
   - `.hive/state.yaml` (final status)
10. Return a concise CEO summary with completed waves, active parallel lanes, pending lanes, blockers, and recommended next action.

## Output Contract

Return this shape in chat:

```markdown
## Hive Summary

- Objective: <text>
- Task Type: <type>
- Current Wave: <wave-id>
- Orchestration Mode: <parallel-wave-dag>
- Active Lanes: <list>
- Ready Queue: <list>
- Team Status:
  - strategy: <status>
  - analysis: <status>
  - architecture: <status>
  - implementation: <status>
  - quality: <status>
  - docs: <status>
  - platform: <status>
- Dependency Health: <ok|degraded|blocked>
- Decisions Needed: <none|list>
- Blockers: <none|list>
- Artifacts:
  - .hive/state.yaml
  - .hive/summary.md
```

## Notes

- This command is orchestration-first and should not bypass decision gates.
- Prefer short parallel waves with explicit lane-level status persistence.
- If state is inconsistent, pause and request CEO decision before continuing.
