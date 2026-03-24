---
description: Run interface-level and function-level micro analysis documentation via interface-analyst
argument-hint: [scope|path|depth]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, TaskOutput, Skill
---

# Interface Analyst

Run the `interface-analyst` agent for micro-level interface/function contract analysis.

## What This Command Does

- Runs `interface-analyst` for interface and function-level contract analysis.
- Generates or refreshes micro documentation under `./.claude/docs/micro/` (or agent-selected output path).
- Returns scope summary, generated files, and prioritized contract risks with improvement items.

## When to Use

Use `/interface-analyst` when:
- You need precise interface/function contracts for integration or refactor preparation
- You need call-chain and dependency touchpoint analysis grounded in code evidence
- You want maintainable micro docs without reading the full source

## How It Works

The interface-analyst agent will:

1. **Parse analysis scope** from `$ARGUMENTS` (path/depth/include-internal)
2. **Discover interfaces and functions** with concrete code evidence
3. **Generate micro docs** under `./.claude/docs/micro/` (or configured output path)
4. **Return actionable summary** with generated files, top risks, and improvements

## Example Usage

### Input

`/interface-analyst commands/workflow-suite depth=standard include-internal=false`

### Example Output

```markdown
${the `interface-analyst` agent output here}
```

## Notes

- Focus on interfaces, functions, call chains, and integration contracts.
- Prefer concrete evidence and file references in output.
