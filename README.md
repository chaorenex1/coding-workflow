# Coding Workflow - AI 智能工作流系统

Claude Code 插件工具库，提供可组合的 Skills、Agents 和 Slash Commands，用于把分析、规划、实现、审查和文档同步组织成可执行工作流。

![Python](https://img.shields.io/badge/python-3.8+-blue.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)
![Version](https://img.shields.io/badge/version-3.1.0-brightgreen.svg)

## 项目概述

Coding Workflow 是一个面向 Claude Code 的插件式工作流系统。它不提供单一的大而全代理，而是通过一组可自动发现的技能、代理和命令，把常见开发任务拆成可复用的执行单元。

当前版本聚焦三件事：

- 多后端协作：为规划、实现、审查等阶段选择合适的 AI 后端
- 工作流编排：通过命令和代理把复杂任务拆解成可跟踪步骤
- 组织化治理：通过 Hive 机制把代理提升为可调度团队

## 当前组件

仓库当前公开的核心组件规模：

- 18 个 skills
- 18 个 agents
- 18 个 commands
- 2 个 hooks

这些组件通过目录约定自动发现，不依赖额外注册表。

## 核心能力

- 需求分析与实施规划
- 多后端实现编排
- TDD 驱动的开发流程
- 质量审查与安全检查
- 仓库级分析和接口级分析
- 文档同步与知识沉淀
- Hive 团队协作治理

## 快速开始

### 方式 1：通过 Claude Code Plugin Marketplace 安装

在 Claude Code 中运行：

```bash
claude plugin marketplace add chaorenex1/coding-workflow
```

然后在 Claude Code 插件界面安装插件。

### 方式 2：本地开发模式

```bash
git clone https://github.com/chaorenex1/coding-workflow.git
cd coding-workflow
```

### 依赖安装

本仓库当前要求以下依赖：

```bash
npm install -g memex-cli
pip install chardet pyyaml
```

依赖会在 SessionStart 阶段通过 [hooks/hooks.json](hooks/hooks.json) 中定义的检查逻辑自动验证。

### 本地验证

```bash
python -m json.tool .claude-plugin/plugin.json
claude code --plugin-dir .
memex-cli --version
python -c "import chardet, yaml; print('OK')"
```

## 目录结构

```text
coding-workflow/
├── .claude-plugin/            # 插件元数据
├── agents/                    # 专业代理
├── commands/                  # Slash Commands
│   ├── scaffold/
│   └── workflow-suite/
├── docs/
│   ├── REPO/                  # 仓库级分析文档
│   └── kb/                    # 知识库与设计文档
├── hooks/                     # SessionStart / PreToolUse hooks
├── prompts/                   # 提示词模板与 Codex 配置输出
├── skills/                    # 可自动发现的技能模块
├── .hive/                     # Hive 状态与模板资产
├── CLAUDE.md                  # 仓库内开发约定
└── README.md
```

## 关键模块

### Skills

当前 skills 目录包含面向不同场景的能力模块，例如：

- [skills/memex-cli/SKILL.md](skills/memex-cli/SKILL.md)：多后端任务执行约定
- [skills/repo-analyzer/SKILL.md](skills/repo-analyzer/SKILL.md)：仓库分析
- [skills/api-document-generator/SKILL.md](skills/api-document-generator/SKILL.md)：API 文档生成
- [skills/tdd/SKILL.md](skills/tdd/SKILL.md)：TDD 执行约定
- [skills/memex-fallback/SKILL.md](skills/memex-fallback/SKILL.md)：降级与回退策略

### Agents

当前 agents 体系已收敛到偏治理与执行编排的角色，例如：

- [agents/analysis-planner.md](agents/analysis-planner.md)：标准实施规划
- [agents/mult-analysis-planner.md](agents/mult-analysis-planner.md)：多后端规划
- [agents/tdd-coder.md](agents/tdd-coder.md)：单后端 TDD 实现
- [agents/mult-tdd-coder.md](agents/mult-tdd-coder.md)：多后端 TDD 实现
- [agents/quality-reviewer.md](agents/quality-reviewer.md)：质量审查
- [agents/security-checker.md](agents/security-checker.md)：安全审查
- [agents/repo-analyst.md](agents/repo-analyst.md)：仓库级文档分析

### Commands

当前 workflow-suite 主要命令包括以下核心入口，完整列表可直接查看 `commands/` 目录：

- [commands/workflow-suite/hive.md](commands/workflow-suite/hive.md)
- [commands/workflow-suite/hive-status.md](commands/workflow-suite/hive-status.md)
- [commands/workflow-suite/coding-plan.md](commands/workflow-suite/coding-plan.md)
- [commands/workflow-suite/mult-coding-plan.md](commands/workflow-suite/mult-coding-plan.md)
- [commands/workflow-suite/tdd-coder.md](commands/workflow-suite/tdd-coder.md)
- [commands/workflow-suite/mult-tdd-coder.md](commands/workflow-suite/mult-tdd-coder.md)
- [commands/workflow-suite/quality-review.md](commands/workflow-suite/quality-review.md)
- [commands/workflow-suite/repo-analyst.md](commands/workflow-suite/repo-analyst.md)
- [commands/workflow-suite/interface-analyst.md](commands/workflow-suite/interface-analyst.md)
- [commands/workflow-suite/ow.md](commands/workflow-suite/ow.md)

## Hive 协作系统

Hive 是当前版本最重要的组织层能力。它不是替换现有 agents，而是在现有能力之上增加一个团队调度与治理层。

详细设计见：

- [docs/kb/hive-skill-design.md](docs/kb/hive-skill-design.md)
- [docs/kb/repository-knowledge-overview.md](docs/kb/repository-knowledge-overview.md)

### Hive 公开入口

| 命令 | 作用 |
|------|------|
| `/hive` | 启动团队化任务编排 |
| `/hive-status` | 查看 Hive 状态、波次和团队进度 |

### Hive 当前公开契约

- 成员注册表：[.hive/members.yaml](.hive/members.yaml)
- Team lead frontmatter 模板：[.hive/templates/team-lead-frontmatter.yaml](.hive/templates/team-lead-frontmatter.yaml)
- 设计文档：[docs/kb/hive-skill-design.md](docs/kb/hive-skill-design.md)

### Hive 设计目标

- 把现有 agents 组织成可复用团队
- 用统一任务包和状态协议管理协作
- 为复杂任务提供中央调度、状态跟踪和决策关口
- 在不重写现有 agents 的前提下复用当前仓库资产

## 推荐工作流

### 标准规划到实现

1. 使用 `/coding-plan` 生成实施计划
2. 使用 `/tdd-coder` 执行测试优先实现
3. 使用 `/quality-review` 做提交前审查

### 多后端规划到实现

1. 使用 `/mult-coding-plan` 生成多后端计划
2. 使用 `/mult-tdd-coder` 执行多后端 TDD 实现
3. 使用 `/quality-review` 和相关质量流程收尾

### 仓库理解与文档同步

1. 使用 `/repo-analyst` 生成仓库级分析
2. 使用 `/interface-analyst` 生成接口级分析
3. 使用 `/sync-docs` 更新说明文档

### 团队化复杂任务

1. 使用 `/hive` 启动任务
2. 使用 `/hive-status` 跟踪编排进度

## 文档索引

- [docs/kb/README.md](docs/kb/README.md)
- [docs/kb/repository-knowledge-overview.md](docs/kb/repository-knowledge-overview.md)
- [docs/kb/hive-skill-design.md](docs/kb/hive-skill-design.md)
- [docs/REPO/architecture.md](docs/REPO/architecture.md)
- [docs/REPO/backend.md](docs/REPO/backend.md)
- [docs/REPO/frontend.md](docs/REPO/frontend.md)
- [docs/REPO/data.md](docs/REPO/data.md)
- [docs/REPO/dependencies.md](docs/REPO/dependencies.md)

## 开发说明

### 自动发现约定

- skills：`skills/*/SKILL.md`
- agents：`agents/*.md`
- commands：`commands/**/*.md`

只要遵循目录结构和 frontmatter 规范，Claude Code 即可自动发现这些组件。

### Hooks

当前启用两个 hook：

- `SessionStart`：执行依赖检查
- `PreToolUse`：执行工具调用前检查

对应配置见 [hooks/hooks.json](hooks/hooks.json)。

## 版本变化说明

3.1.0 版本完成了一次明显的体系收敛：

- 移除了旧的 BMAD 和 quick-code 工作流资产
- 引入 Hive 协作治理层
- 收敛 workflow-suite 命令集到更直接的规划、实现、审查与分析路径
- 补充了 `docs/kb` 与 `docs/REPO` 作为新的知识和结构说明入口

## 贡献

欢迎提交 issue、文档修正和新能力模块。

基本流程：

1. Fork 仓库
2. 创建分支
3. 提交变更
4. 发起 Pull Request

如果改动涉及 agent、command 或 skill，建议同步更新相应文档和 README 中的公开入口说明。

## 许可证

本项目采用 MIT 许可证，详见 [LICENSE](LICENSE)。

## 致谢

- [Anthropic Claude](https://www.anthropic.com/)
- [Google Gemini](https://ai.google.dev/)
- [OpenAI Codex](https://openai.com/blog/openai-codex)
- [memex-cli](https://github.com/chaorenex1/memex-cli)

## 联系方式

- 问题反馈：GitHub Issues

---

从一句话需求到可执行工作流，Coding Workflow 关注的不只是生成代码，而是把协作、验证和沉淀一起组织起来。
