---
description: Sync and update existing repo analysis documentation by running repo-analyst in update mode
argument-hint: [optional-focus]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, TaskOutput, Skill
---

# Repo Analyst Sync

Update existing repository analysis docs by running `repo-analyst` against current codebase.

## Workflow

1. Parse optional sync focus from `$ARGUMENTS`.
2. Invoke agent: `repo-analyst`.
3. Update existing files in `docs/REPO/` instead of creating alternate files.
4. Summarize:
   - which files changed
   - what sections were refreshed
   - remaining staleness risks

## Expected Targets

- `docs/REPO/architecture.md`
- `docs/REPO/backend.md`
- `docs/REPO/frontend.md`
- `docs/REPO/data.md`
- `docs/REPO/dependencies.md`
