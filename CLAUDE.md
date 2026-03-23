# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Project Overview

**Coding Workflow** is a Claude Code plugin system providing AI-driven development workflows through 18 Skills, 18 Agents, and 18 Commands. It integrates multiple AI backends and a Hive governance layer to coordinate planning, implementation, review, and documentation workflows.

**Repository**: https://github.com/chaorenex1/coding-workflow

---

## Plugin Architecture

This is a **Claude Code Plugin** distributed via Plugin Marketplace. Key structure:

- `.claude-plugin/plugin.json` - Plugin manifest defining all components
- `skills/` - 18 auto-discovered skill modules (each with `SKILL.md`)
- `agents/` - 18 specialized AI agents
- `commands/` - 18 slash commands across scaffold and workflow-suite
- `hooks/hooks.json` - SessionStart hook for dependency validation

### Component Auto-Discovery

Claude Code automatically discovers:
- Skills: All directories in `skills/` with `SKILL.md`
- Agents: All `.md` files in configured agent subdirectories
- Commands: All `.md` files in configured command subdirectories

When adding new components, no manifest update needed if following standard structure.

---

## Dependencies

### Required
- **memex-cli** (npm package): Backend orchestration tool
  ```bash
  npm install -g memex-cli
  ```
- **Python dependencies**: chardet, pyyaml
  ```bash
  pip install chardet pyyaml
  ```

### Validation
Dependencies are auto-checked on SessionStart via `hooks/hooks.json`. Check cache at `~/.claude/coding-workflow-deps-check.txt` (24h TTL).

---

## Development Workflows

### Core Workflow Suite

The current repository no longer uses the BMAD and quick-code workflow families. The supported workflows are centered on planning, TDD execution, review, analysis, and Hive orchestration.

**Standard Plan -> Implement -> Review**
1. `/coding-plan` - Generate a standard implementation plan
2. `/tdd-coder` - Execute strict RED -> GREEN -> REFACTOR implementation
3. `/quality-review` - Review the resulting changes

**Multi-Backend Plan -> Implement -> Review**
1. `/mult-coding-plan` - Generate a multi-backend implementation plan
2. `/mult-tdd-coder` - Coordinate multi-backend TDD implementation
3. `/quality-review` - Run the final review pass

**Repository Analysis and Documentation**
1. `/repo-analyst` - Generate repository-level macro analysis
2. `/interface-analyst` - Generate interface/function-level micro analysis
3. `/sync-docs` - Refresh documentation after changes

### Hive Governance Workflow

Hive adds an organizational coordination layer above the existing agents and commands.

Key public artifacts:
```
.hive/
├── members.yaml                        # Member registry
└── templates/
  └── team-lead-frontmatter.yaml      # Team lead template
```

Primary entry points:
- `/hive` - launch Hive orchestration
- `/hive-status` - inspect current Hive status

---

## Common Commands

### Plugin Development

```bash
# Validate plugin structure
python -m json.tool .claude-plugin/plugin.json

# Test plugin locally (from plugin directory)
claude code --plugin-dir .

# Check dependencies
memex-cli --version
python -c "import chardet, yaml"
```

### Creating New Components

**New Skill**:
```bash
mkdir skills/my-skill
cat > skills/my-skill/SKILL.md <<EOF
---
name: my-skill
description: Brief description when to use this skill
---
# Skill implementation instructions
EOF
```

**New Agent**:
```bash
cat > agents/my-agent.md <<EOF
---
name: my-agent
description: Agent role and when to invoke
tools: Read, Write, Edit, Bash, Grep, Glob
model: sonnet
---
# Agent system prompt
EOF
```

**New Command**:
```bash
cat > commands/workflow-suite/my-command.md <<EOF
---
name: my-command
description: Command purpose
---
# Command implementation
EOF
```

### Git Workflows

```bash
# Standard commit with co-authorship
git commit -m "$(cat <<'EOF'
feat: description

Details

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
EOF
)"

# Create release tag
git tag -a v3.0.1 -m "Release: Description"
git push origin main --tags
```

---

## Architecture Patterns

### Multi-Backend Orchestration

Commands like `/mult-coding-plan` and `/mult-tdd-coder` orchestrate specialized backends for planning and implementation:
- **Claude**: orchestration, planning synthesis, and acceptance decisions
- **Gemini**: UI and interaction-oriented execution paths
- **Codex**: implementation-heavy code generation and refactoring paths

Backend selection managed by orchestrator commands using `memex-cli` with `--backend` flag.

### Agent Coordination

Coordinator agents and commands manage:
- Planning and implementation handoff
- Specialized agent invocation via Task tool
- State persistence through `.hive/` and generated docs when applicable
- Progress tracking and review reporting

### Skill Invocation

Skills are triggered two ways:
1. **Explicit**: `/skill skill-name "task description"`
2. **Auto-invoked**: Claude Code matches task context to skill description

---

## Key Files and Locations

### Plugin Configuration
- `.claude-plugin/plugin.json` - Plugin manifest (DO NOT modify component paths unless changing structure)
- `hooks/hooks.json` - Event hooks (SessionStart dependency check)
- `.hive/members.yaml` - Hive member registry

### Documentation
- `README.md` - Public-facing documentation
- `docs/kb/hive-skill-design.md` - Hive design documentation
- `docs/REPO/` - Repository-level generated architecture docs
- Individual `SKILL.md` files - Skill-specific documentation

### Ignore Patterns
`.gitignore` excludes:
- `.claude/*.local.md` - User configurations
- `.claude/coding-workflow-deps-check.txt` - Dependency check cache
- `orchestrator_output/` - Execution artifacts

---

## Workflow Selection Guide

| Scenario | Command | Use When |
|----------|---------|----------|
| Standard implementation planning | `/coding-plan` | Produce a single-backend implementation plan |
| Multi-backend implementation planning | `/mult-coding-plan` | Plan work spanning Codex/Gemini style execution |
| Standard TDD implementation | `/tdd-coder` | Execute strict test-first feature or fix work |
| Multi-backend TDD implementation | `/mult-tdd-coder` | Coordinate test-first work across multiple backends |
| Code review | `/quality-review` | Review current changes for quality and maintainability |
| Repository macro analysis | `/repo-analyst` | Generate repository-level architecture docs |
| Interface micro analysis | `/interface-analyst` | Analyze function and interface contracts |
| Team orchestration | `/hive` | Run the Hive governance workflow |
| Hive status tracking | `/hive-status` | Inspect current Hive orchestration state |

---

## Best Practices

### Before Making Changes
1. Use `/repo-analyst` or `/interface-analyst` to understand structure when needed
2. Read relevant agent and skill documentation
3. Prefer planning before implementation for non-trivial changes

### Adding Components
1. Follow naming conventions (kebab-case)
2. Place in correct subdirectory per component type
3. Include YAML frontmatter with required fields
4. Test auto-discovery with `claude code --plugin-dir .`

### Plugin Testing
Follow the local validation flow:
1. Validate JSON manifests
2. Test local plugin loading
3. Verify component discovery against the current repository state
4. Check dependency validation hook

### Documentation Sync
When modifying code affecting architecture or user workflows:
1. Update relevant `.md` files in `docs/`
2. Consider invoking `documentation-sync-agent` for automated sync
3. Maintain consistency across README, ARCHITECTURE, and component docs

---

## Troubleshooting

### Plugin Not Loading
```bash
# Verify manifest syntax
python -m json.tool .claude-plugin/plugin.json

# Check directory structure
ls -la skills/*/SKILL.md | wc -l
find agents -name "*.md" | wc -l
find commands -name "*.md" | wc -l
```

### Dependency Issues
```bash
# Force re-check (delete cache)
rm ~/.claude/coding-workflow-deps-check.txt

# Manual verification
which memex-cli || where memex-cli
python -c "import chardet, yaml; print('OK')"
```

### Component Not Discovered
- Verify file naming: `SKILL.md` for skills (case-sensitive)
- Check YAML frontmatter syntax
- Ensure file in correct subdirectory per `plugin.json` paths
- Restart Claude Code session

---

## Version Management

Current version: **3.1.0**

Semantic versioning (MAJOR.MINOR.PATCH):
- MAJOR: Breaking changes to plugin structure or API
- MINOR: New skills/agents/commands (backward compatible)
- PATCH: Bug fixes, documentation updates

Update version in:
1. `.claude-plugin/plugin.json`
2. `README.md` badges
3. Create git tag: `git tag -a v3.1.x -m "Release notes"`
