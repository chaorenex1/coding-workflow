---
name: memex-cli
description: "Execute AI tasks (codex/claude/gemini) with memory and resume support via memex-cli stdin protocol."
---

# Memex CLI

A CLI wrapper for AI backends (Codex, Claude, Gemini) with built-in memory and resume capabilities.

## Core Concepts

memex-cli uses **stdin protocol** to define tasks, allowing:
- Multi-backend AI execution (codex, claude, gemini)
- Parallel and sequential task orchestration
- Resume from previous runs with full context

## Basic Task Syntax

```bash
memex-cli run --stdin <<'EOF'
---TASK---
id: <task_id>
backend: <backend>
workdir: <working_directory>
timeout: 3000000
---CONTENT---
<task content here>
---END---
EOF
```

### Required Parameters

| Parameter | Description | Example |
|-----------|-------------|---------|
| `id` | Unique task identifier | `implement-auth-20260110` |
| `backend` | AI backend | `codex`, `claude`, `gemini` |
| `workdir` | Working directory path | `<working_directory>` or `/home/user/app` |
| `<TASK_CONTENT>` | Step by step instructions for the task | `Implement authentication module` |

### Optional Parameters

| Parameter | Description  | Example |
|-----------|-------------|---------|
| `role_prompt` | Task role prompt path | `prompts/developer_role.md` |
| `dependencies` | Task dependencies | `task-a` or `task-a,task-b` |
| `timeout` | Timeout in seconds | `3000000`|
| `files` | File Or Directory paths to load | `src/**/*.py` (glob supported) |


## Task Content Generate

Write task content as explicit, step-by-step instructions with clear outputs.

### Recommended Structure

1. Objective: What must be achieved.
2. Scope: Which files, modules, or boundaries are included.
3. Steps: Ordered implementation or analysis actions.
4. Output: What artifacts or results must be returned.
5. Validation: How to verify correctness.

### Template

```text
Objective:
- <target outcome>

Scope:
- <file/module boundaries>

Steps:
1. <step 1>
2. <step 2>
3. <step 3>

Output:
- <expected deliverable>

Validation:
- <verification criteria>
```

### Example: Single Task Content

```text
Objective:
- Implement JWT authentication for login and protected routes.

Scope:
- src/auth/**
- src/middleware/**

Steps:
1. Add token generation and verification utilities.
2. Add auth middleware for protected endpoints.
3. Add input validation and error handling.

Output:
- Updated authentication module and middleware.
- Brief summary of changed files and behavior.

Validation:
- Login returns a valid token.
- Protected route rejects invalid or missing tokens.
```

### Example: Multi-Task Content Split

Use focused task content per task when using multi-task execution:

- Planning task: architecture and implementation plan only.
- Implementation task: code changes only, based on approved plan.
- Validation task: tests, review, and quality checks only.

### Quality Checklist

- Avoid vague verbs such as "improve" or "optimize" without measurable criteria.
- Include explicit constraints (performance, compatibility, security) when required.
- Keep each task content scoped so completion can be clearly verified.


## Backend Selection Guide

### Codex

Specialized in deep code analysis, large-scale refactoring, and performance optimization.

- **Deep Code Analysis & Understanding** — Analyze complex implementations with comprehensive understanding, navigate intricate dependencies, and identify architectural patterns in mixed-language codebases.
- **Large-Scale Refactoring** — Execute precise refactoring across multiple files with accurate dependency tracking, ensuring no references are broken during structural transformations.
- **Algorithm & Performance Optimization** — Identify performance bottlenecks, optimize algorithms, and provide detailed optimization strategies with measurable improvements.

### Claude

Specialized in fast feature delivery from clear requirements, technical documentation design, and professional prompt engineering.

- **Quick Feature Implementation from Clear Requirements** — Quickly translate well-defined product or engineering requirements into practical implementation plans and deliver features efficiently.
- **Technical Documentation Design & Writing** — Produce clear, professional technical documents such as API specifications, integration guides, architecture notes, and README files.
- **Professional Prompt Engineering** — Craft high-quality prompts for product requirements, design specifications, workflow orchestration, and other structured AI-assisted development tasks.

### Gemini

- **UI Skeletons & Layout Prototyping** — Build clear UI skeletons and layout prototypes to quickly validate information architecture and page structure.
- **Consistent Design System Implementation** — Implement interfaces with a consistent design language, including reusable components, spacing rules, and visual patterns.
- **Interactive and Accessible Elements** — Create interactive, accessible UI elements with clear states, keyboard support, and usability-focused interactions.

## Task ID Patterns

**Recommended patterns:**

```
# Timestamp format (unique)
task-20260110143052
implement-auth-20260110143052

# Semantic format (readable)
design-api
implement-backend
test-integration

# Hierarchical format (organized)
auth.design
auth.implement
auth.test
```

Avoid generic IDs like `task1`, `task2`.

## Role Prompt

Use `role_prompt` to attach a predefined role instruction file to a task.

### When to Use

- Enforce consistent behavior across repeated tasks.
- Apply domain-specific expertise (for example, backend engineer, QA reviewer, or API architect).
- Separate reusable role guidance from task-specific content.

### Format

- `role_prompt` should be a readable file path relative to `workdir`.
- Keep role prompt files concise and focused on role behavior, constraints, and output style.

### Example

```bash
memex-cli run --stdin <<'EOF'
---TASK---
id: implement-auth-with-role
backend: codex
workdir: <working_directory>
role_prompt: prompts/developer_role.md
timeout: 3000000
---CONTENT---
Implement JWT-based authentication with input validation and tests.
---END---
EOF
```

### Best Practices

- Reuse stable role prompt files instead of duplicating instructions in every task.
- Keep task content focused on concrete goals; keep role behavior in `role_prompt`.
- Version and review role prompt files like code to maintain quality.

## File References

Use `files` to load relevant context into a task. You can reference single files, directories, or glob patterns.

### Common Reference Patterns

- Single file: `files: ./README.md`
- Directory: `files: ./docs/`
- Glob pattern: `files: src/**/*.ts`
- Multiple `files` entries: use commas to separate paths

### Example

```bash
memex-cli run --stdin <<'EOF'
---TASK---
id: api-doc-review
backend: claude
workdir: <working_directory>
timeout: 3000000
files: ./README.md,./docs/api/*.md
---CONTENT---
Review API documentation consistency and suggest improvements.
---END---
EOF
```

## Multi-Task Execution

Run multiple tasks in a single stdin payload. memex-cli automatically schedules tasks by dependency:

- Tasks without `dependencies` run in parallel.
- Tasks with `dependencies` run after their prerequisite tasks complete.

### Example A: Parallel Execution

Use this when tasks are independent and can run at the same time.

```bash
memex-cli run --stdin <<'EOF'
---TASK---
id: analyze-frontend
backend: codex
workdir: <working_directory>
timeout: 3000000
---CONTENT---
Analyze the frontend module and list refactoring opportunities.
---END---

---TASK---
id: review-api-contract
backend: claude
workdir: <working_directory>
timeout: 3000000
---CONTENT---
Review API contract consistency and propose improvements.
---END---
EOF
```

### Example B: DAG (Dependency-Aware) Execution

Use this when later tasks depend on earlier outputs.

```bash
memex-cli run --stdin <<'EOF'
---TASK---
id: plan-feature
backend: claude
workdir: <working_directory>
timeout: 3000000
---CONTENT---
Create an implementation plan for the notification feature.
---END---

---TASK---
id: implement-feature
backend: codex
workdir: <working_directory>
timeout: 3000000
dependencies: plan-feature
---CONTENT---
Implement the feature based on the approved plan.
---END---

---TASK---
id: validate-ui
backend: gemini
workdir: <working_directory>
timeout: 3000000
dependencies: implement-feature
files: ./mockups/notification/*.png
---CONTENT---
Validate UI consistency and accessibility.
---END---
EOF
```

**Execution modes:**
- **Parallel (default)**: Independent tasks execute simultaneously for faster completion.
- **DAG (dependency-aware)**: Tasks execute in dependency order when `dependencies` is set.

## Return Format

```text
CLI response text here...

---
RUN_ID: 019a7247-ac9d-71f3-89e2-a823dbd8fd14


```

## Resume Functionality

Continue from a previous run using `--run-id`:

```bash

# Resume from that run
memex-cli resume --run-id <run_id> --stdin <<'EOF'
---TASK---
id: continue
backend: codex
workdir: <working_directory>
timeout: 3000000
---CONTENT---
基于之前的实现添加功能
---END---
EOF
```

**Context preservation:**
- Previous task outputs available
- Conversation history maintained
- File changes visible

## Invocation Pattern

### Single Task

Use this pattern for one independent task.

```bash
memex-cli run --stdin <<'EOF'
---TASK---
id: <single_task_id>
backend: <codex|claude|gemini>
workdir: <working_directory>
timeout: 3000000
---CONTENT---
<clear task instruction>
---END---
EOF
```

### Multi-Task

Use this pattern when you need parallel or dependency-aware execution.

```bash
memex-cli run --stdin <<'EOF'
---TASK---
id: <task_a_id>
backend: <codex|claude|gemini>
workdir: <working_directory>
timeout: 3000000
---CONTENT---
<task A instruction>
---END---

---TASK---
id: <task_b_id>
backend: <codex|claude|gemini>
workdir: <working_directory>
timeout: 3000000
dependencies: <task_a_id>
---CONTENT---
<task B instruction>
---END---
EOF
```

### Session Resume

Use this pattern to continue an existing run with preserved context.

```bash
memex-cli resume --run-id <run_id> --stdin <<'EOF'
---TASK---
id: <continue_task_id>
backend: <codex|claude|gemini>
workdir: <working_directory>
timeout: 3000000
---CONTENT---
<follow-up instruction>
---END---
EOF
```

## Critical Rules

- Never kill any memex-cli process manually.
- Always wait for TaskOutput and check task output before making decisions.
- Always verify that memex-cli processes have not been killed.

## Security 

- Do not expose sensitive information in task instructions or outputs.
- Ensure file paths and data references are secure and access-controlled.
- Regularly review and update dependencies to mitigate vulnerabilities.
- Task instructions should avoid including sensitive information such as passwords, API keys, personal data, or permission-related details.

