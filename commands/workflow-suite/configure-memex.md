---
description: Install or re-install memex-cli using the official one-line installer for current OS
allowed-tools: Bash
---

# Configure Memex

Install `memex-cli` with the official installer command for your platform.

## Commands

1. Linux / macOS (Shell)

```bash
curl -sSL https://github.com/chaorenex1/memex-cli/releases/latest/download/install_memex.sh | bash
```

2. Windows (PowerShell)

```powershell
irm https://github.com/chaorenex1/memex-cli/releases/latest/download/install_memex.ps1 | iex
```

## Verification

After installation, verify:

```bash
memex-cli --version
```

## ECC And Coding-Workflow Setup

Follow this sequence to reduce mistakes and make the setup reproducible.

### Phase A: Chat Commands (Not Terminal Commands)

Run these two lines in Claude chat input (do not run in Bash/PowerShell):

1. `/plugin marketplace add affaan-m/everything-claude-code`
2. `/plugin install everything-claude-code@everything-claude-code`

### Phase B: Preflight Checks

Linux/macOS (Bash):

```bash
test -d ~/.claude/plugins/marketplaces/everything-claude-code
test -d ~/.claude/plugins/marketplaces/coding-workflow
mkdir -p ~/.claude/rules ~/.codex/skills ~/.codex/agents
```

Windows (PowerShell):

```powershell
Test-Path "$HOME/.claude/plugins/marketplaces/everything-claude-code"
Test-Path "$HOME/.claude/plugins/marketplaces/coding-workflow"
New-Item -ItemType Directory -Force -Path "$HOME/.claude/rules","$HOME/.codex/skills","$HOME/.codex/agents" | Out-Null
```

### Phase C: Strict Mode And Backup

Linux/macOS (Bash):

```bash
set -euo pipefail
ts=$(date +%Y%m%d-%H%M%S)
backup_dir="$HOME/.codex-backup-$ts"
mkdir -p "$backup_dir"
cp -a ~/.claude/CLAUDE.md "$backup_dir/CLAUDE.md" 2>/dev/null || true
cp -a ~/.codex/AGENTS.md "$backup_dir/AGENTS.md" 2>/dev/null || true
cp -a ~/.codex/config.toml "$backup_dir/config.toml" 2>/dev/null || true
```

Windows (PowerShell):

```powershell
$ErrorActionPreference = "Stop"
$ts = Get-Date -Format "yyyyMMdd-HHmmss"
$backupDir = Join-Path $HOME ".codex-backup-$ts"
New-Item -ItemType Directory -Force -Path $backupDir | Out-Null
if (Test-Path "$HOME/.claude/CLAUDE.md") { Copy-Item "$HOME/.claude/CLAUDE.md" "$backupDir/CLAUDE.md" -Force }
if (Test-Path "$HOME/.codex/AGENTS.md") { Copy-Item "$HOME/.codex/AGENTS.md" "$backupDir/AGENTS.md" -Force }
if (Test-Path "$HOME/.codex/config.toml") { Copy-Item "$HOME/.codex/config.toml" "$backupDir/config.toml" -Force }
```

### Phase D: File Sync Steps

3) Copy rules from marketplace to local Claude rules.

Linux/macOS (Bash):

```bash
cp -r ~/.claude/plugins/marketplaces/everything-claude-code/rules/* ~/.claude/rules/
```

Windows (PowerShell):

```powershell
Copy-Item "$HOME/.claude/plugins/marketplaces/everything-claude-code/rules/*" "$HOME/.claude/rules/" -Recurse -Force
```

4) Copy skills from marketplace to local Codex skills.

Linux/macOS (Bash):

```bash
cp -r ~/.claude/plugins/marketplaces/everything-claude-code/.agents/skills/* ~/.codex/skills/
```

Windows (PowerShell):

```powershell
Copy-Item "$HOME/.claude/plugins/marketplaces/everything-claude-code/.agents/skills/*" "$HOME/.codex/skills/" -Recurse -Force
```

5) Copy and overwrite CLAUDE.md.

Linux/macOS (Bash):

```bash
cp ~/.claude/plugins/marketplaces/coding-workflow/prompts/.claude/CLAUDE.md ~/.claude/CLAUDE.md
```

Windows (PowerShell):

```powershell
Copy-Item "$HOME/.claude/plugins/marketplaces/coding-workflow/prompts/.claude/CLAUDE.md" "$HOME/.claude/CLAUDE.md" -Force
```

6) Copy and overwrite AGENTS.md.

Linux/macOS (Bash):

```bash
cp ~/.claude/plugins/marketplaces/coding-workflow/prompts/.codex/AGENTS.md ~/.codex/AGENTS.md
```

Windows (PowerShell):

```powershell
Copy-Item "$HOME/.claude/plugins/marketplaces/coding-workflow/prompts/.codex/AGENTS.md" "$HOME/.codex/AGENTS.md" -Force
```

7) Copy codex agents directory.

Linux/macOS (Bash):

```bash
cp -r ~/.claude/plugins/marketplaces/coding-workflow/prompts/.codex/agents ~/.codex/
```

Windows (PowerShell):

```powershell
Copy-Item "$HOME/.claude/plugins/marketplaces/coding-workflow/prompts/.codex/agents" "$HOME/.codex/" -Recurse -Force
```

### Phase E: Config Merge (Step 8)

Compare both files first.

Linux/macOS (Bash):

```bash
diff -u ~/.codex/config.toml ~/.claude/plugins/marketplaces/coding-workflow/prompts/.codex/config.toml || true
```

Windows (PowerShell):

```powershell
git diff --no-index "$HOME/.codex/config.toml" "$HOME/.claude/plugins/marketplaces/coding-workflow/prompts/.codex/config.toml"
```

Merge policy (to avoid accidental breakage):

1. Only add keys that do not exist in local `~/.codex/config.toml`.
2. Keep local values for existing keys unless user explicitly approves overwrite.
3. For array/table conflicts, append only new entries and keep existing order.
4. Record all newly added keys in the final summary.

### Phase F: Final Validation And Summary (Step 9)

Linux/macOS (Bash):

```bash
ls -la ~/.claude/rules
ls -la ~/.codex/skills
ls -la ~/.codex/agents
grep -n "Selena - Expert Software Engineering Assistant" ~/.claude/CLAUDE.md
grep -n "ECC for Codex CLI" ~/.codex/AGENTS.md
```

Windows (PowerShell):

```powershell
Get-ChildItem "$HOME/.claude/rules"
Get-ChildItem "$HOME/.codex/skills"
Get-ChildItem "$HOME/.codex/agents"
Select-String -Path "$HOME/.claude/CLAUDE.md" -Pattern "Selena - Expert Software Engineering Assistant"
Select-String -Path "$HOME/.codex/AGENTS.md" -Pattern "ECC for Codex CLI"
```

Final report template:

- Steps completed: 1-9 with pass/fail per step
- Overwrites performed: list of overwritten files
- Added config keys: exact key list
- Backup path: backup directory created in Phase C

### Rollback

If you need to roll back, restore from the backup created in Phase C.

Linux/macOS (Bash):

```bash
cp -a "$backup_dir/CLAUDE.md" ~/.claude/CLAUDE.md 2>/dev/null || true
cp -a "$backup_dir/AGENTS.md" ~/.codex/AGENTS.md 2>/dev/null || true
cp -a "$backup_dir/config.toml" ~/.codex/config.toml 2>/dev/null || true
```

Windows (PowerShell):

```powershell
if (Test-Path "$backupDir/CLAUDE.md") { Copy-Item "$backupDir/CLAUDE.md" "$HOME/.claude/CLAUDE.md" -Force }
if (Test-Path "$backupDir/AGENTS.md") { Copy-Item "$backupDir/AGENTS.md" "$HOME/.codex/AGENTS.md" -Force }
if (Test-Path "$backupDir/config.toml") { Copy-Item "$backupDir/config.toml" "$HOME/.codex/config.toml" -Force }
```
