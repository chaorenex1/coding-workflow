---
name: memex-fallback
description: "Fallback to memex-cli when codeagent-wrapper is unavailable. Automatically converts codeagent-wrapper syntax to memex-cli stdin protocol and executes."
---

# Memex Fallback

Automatically detect codeagent-wrapper availability and convert to memex-cli when needed.

## Detection Logic

```bash
# Check if codeagent-wrapper exists
if command -v codeagent-wrapper &> /dev/null; then
  # Use codeagent-wrapper directly
  codeagent-wrapper {{LITE_MODE_FLAG}}--backend <backend> {{GEMINI_MODEL_FLAG}}- "$PWD" <<'EOF'
  ROLE_FILE: <role prompt path>
  <TASK>
  <content>
  </TASK>
  OUTPUT: <output requirement>
  EOF
else
  # Fallback to memex-cli with parameter conversion
  invoke memex-cli bridge mode
fi
```

## Parameter Conversion

### codeagent-wrapper → memex-cli

| codeagent-wrapper | memex-cli stdin |
|-------------------|-----------------|
| `--backend <backend>` | `backend: <backend>` |
| `--model <model>` | (use default or pass via content) |
| `"$PWD"` | `workdir: <working_directory>` |
| `ROLE_FILE: <path>` | `role_prompt: <path>` |
| `<TASK>...</TASK>` | `---CONTENT---...---END---` |
| `OUTPUT: <req>` | (include in ---CONTENT---) |
| `{{LITE_MODE_FLAG}}` | (ignore, not applicable) |
| `{{GEMINI_MODEL_FLAG}}` | (ignore, not applicable) |

## memex-cli Invocation

```bash
memex-cli run --stdin <<'EOF'
---TASK---
id: <unique_task_id>
backend: <codex|gemini|claude>
workdir: <working_directory>
role_prompt: <role_prompt_path>
timeout: 3600000
---CONTENT---
<TASK>
<content from original TASK block>
</TASK>
OUTPUT: <output requirement from original OUTPUT line>
---END---
EOF
```

## Task ID Generation

Generate unique task ID:
- Format: `<feature>-<timestamp>`
- Example: `auth-impl-20260322-143052`

## Exit Handling

- **Success:** Return memex-cli output directly
- **Failure (exit code ≠ 0):**
  1. Stop execution
  2. Report: failure reason, exit code, error output
  3. Wait for explicit user instruction
  4. NO automatic fallback

## Security Notes

- Do not expose sensitive information in task instructions
- Verify file paths are within expected directories
- Task instructions should not include passwords, API keys, or personal data
