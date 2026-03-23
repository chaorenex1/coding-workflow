# Dependency Macro Analysis

## Runtime Dependencies

```text
Required:
- Python 3.8+
- memex-cli (npm global package)
- Python packages: chardet, pyyaml

Implicit:
- Claude Code runtime and plugin loading support
```

## Toolchain Dependencies

- Python for hook scripts and many skill scripts
- npm for global memex-cli installation
- GitHub repository hosting and release workflow

## External Integrations

```text
+--------------------+------------------------------+
| Integration        | Purpose                      |
+--------------------+------------------------------+
| Claude Code        | Host runtime and tool model  |
| memex-cli          | Backend orchestration bridge |
| GitHub             | Source control and delivery  |
| Optional AI backends | model routing by workflows |
+--------------------+------------------------------+
```

## Shared Libraries and Contracts

- Shared contract style: markdown frontmatter + procedural instructions
- Shared validation points: hooks/check-deps.py and hooks/pre-tool-use.py
- Shared metadata anchor: .claude-plugin/plugin.json

## Dependency Flow (ASCII)

```text
System runtime
  -> Claude Code host
      -> plugin metadata
      -> hooks
      -> commands and agents
      -> skills (Python scripts and docs)
      -> optional memex-cli backend orchestration
```

## Upgrade Priorities

1. Keep memex-cli compatibility documented and tested.
2. Keep Python dependency checks aligned with scripts used in skills/hooks.
3. Add explicit version matrix for Claude Code capabilities vs workflow features.
4. Periodically audit optional integrations referenced in docs.
