---
description: Run repo-level macro analysis and generate docs under docs/REPO via repo-analyst
argument-hint: [optional-scope-or-focus]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, TaskOutput, Skill
---

# Repo Analyst

Run the `repo-analyst` agent to generate or refresh repository-level macro analysis docs.

## Workflow

1. Parse user input from `$ARGUMENTS` as optional focus.
2. Invoke agent: `repo-analyst`.
3. Ensure outputs are written to `docs/REPO/`:
   - `architecture.md`
   - `backend.md`
   - `frontend.md`
   - `data.md`
   - `dependencies.md`
4. Return a concise summary of updated files and key deltas.

## Notes

- Prefer updating existing files instead of creating parallel duplicates.
- Keep diagrams in ASCII format.
