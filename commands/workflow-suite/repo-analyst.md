---
description: Run repo-level macro analysis and generate docs under docs/REPO via repo-analyst
argument-hint: [optional-scope-or-focus]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, TaskOutput, Skill
---

# Repo Analyst

Run the `repo-analyst` agent to generate or refresh repository-level macro analysis docs.

## What This Command Does

- Runs the `repo-analyst` agent to generate or refresh repository-level macro analysis docs.
- Produces or updates the five canonical documents under `docs/REPO/`.

## When to Use

Use `/repo-analyst` when:
- You need a fresh macro architecture snapshot for onboarding or review
- The repository changed and `docs/REPO/` may be stale
- You want all five repo-level documents refreshed in one run

## How It Works

The repo-analyst agent will:

1. **Parse analysis focus** from `$ARGUMENTS` (optional)
2. **Scan repository context** and infer macro structure
3. **Generate or refresh**:
   - `docs/REPO/architecture.md`
   - `docs/REPO/backend.md`
   - `docs/REPO/frontend.md`
   - `docs/REPO/data.md`
   - `docs/REPO/dependencies.md`
4. **Return update details** with changed targets and key deltas

## Example Usage

### Input

`/repo-analyst plugin architecture and workflow routing`

### Example Output

```markdown
${the `repo-analyst` agent output here}
```

## Notes

- Prefer updating existing files instead of creating parallel duplicates.
- Keep diagrams in ASCII format.
