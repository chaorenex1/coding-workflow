# Frontend Macro Analysis

## User Interaction Surface

This repository's frontend is primarily conversational UX inside Claude Code.
There is no standalone React/Vue web UI in this repository root.

Primary interaction surfaces:
- Slash commands
- Agent invocation
- Skill-guided task flows
- Generated markdown artifacts in docs/

## Page/Command Tree (ASCII)

```text
Claude Code Chat UI
|
+-- /commands
|   +-- scaffold/
|   |   +-- electron-scaffold.md
|   |   +-- project-scaffold.md
|   |
|   +-- workflow-suite/
|       +-- coverage.md
|       +-- dead-code-clean.md
|       +-- ow.md
|       +-- quick_feature.md
|       +-- sync-docs.md
|
+-- Agents
|   +-- analysis and architecture agents
|   +-- coding and quality agents
|
+-- Skills
    +-- analysis skills
    +-- code generation/refactoring skills
    +-- testing and validation skills
```

## Component Hierarchy (Conceptual)

```text
User Prompt
  -> Command Layer
      -> Agent Layer
          -> Skill Layer
              -> Tool Calls
                  -> File Outputs
```

## State Management Flow

```text
Input state: user request + workspace context
  -> planning/analysis state (agent and command markdown contracts)
  -> execution state (tool calls, hook checks)
  -> artifact state (updated files, generated docs)
```

## UX Notes

- UX depends heavily on clarity and consistency of markdown instructions.
- Discoverability is tied to command naming and documentation quality.
- Fast onboarding requires stable naming and examples across commands and skills.

## UX Risks

- Overlapping command purposes can confuse users.
- Long instruction files may reduce task success rate.
- Inconsistent language style across modules increases cognitive load.
