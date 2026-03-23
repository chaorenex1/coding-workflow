# Coding Workflow - AI 智能工作流系统

**Claude Code 扩展工具库，包含 Skills、Agents 和 Commands (Slash Commands)，用于增强 Claude Code 的能力**

![Python](https://img.shields.io/badge/python-3.8+-blue.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)
![Status](https://img.shields.io/badge/status-production-brightgreen.svg)

## 📖 项目概述

Coding Workflow 是一个面向开发者的 AI 智能工作流系统，实现从需求分析、架构设计、代码实现到测试发布的端到端自动化开发流程。

**核心价值**：
- 🤖 **多后端协调** - 选择最适合的 AI 后端执行任务
- 🔄 **端到端自动化** - 从一句话需求到完整代码实现
- 🛠️ **18 项技能集成** - 代码分析、重构、文档生成等专业技能
- 🏃 **零开销执行** - 90% 任务直接执行，无路由开销

## 🚀 快速开始

### 安装

#### 方式 1: 通过 Claude Code Plugin Marketplace（推荐）

**一键安装**：

在 Claude Code 中运行：
```
claude plugin marketplace add chaorenex1/coding-workflow
```

在Claude Code插件界面安装所有技能、代理和命令。

**依赖安装**：

插件启动时会自动检查依赖，如有缺失会提示安装：

## 🐝 蜂巢协作系统

> **状态：设计已公开，命令入口已提供。** 详细方案见 [docs/kb/hive-skill-design.md](docs/kb/hive-skill-design.md)。

蜂巢系统在现有 agents、commands、skills 之上增加组织治理层，将独立代理组织为可调度团队，通过统一任务包、团队回报和决策关口来处理复杂任务。

### 蜂巢命令

| 命令 | 用途 | 当前状态 |
|------|------|----------|
| `/hive` | 启动蜂巢工作流，按团队分派任务 | 已提供入口（commands/workflow-suite/hive.md） |
| `/hive-status` | 查询蜂巢任务状态和团队进度 | 已提供入口（commands/workflow-suite/hive-status.md） |

### 当前公开契约

- 成员注册表：`.hive/members.yaml`
- Team lead frontmatter 模板：`.hive/templates/team-lead-frontmatter.yaml`
- 设计文档：[docs/kb/hive-skill-design.md](docs/kb/hive-skill-design.md)

## 🤝 贡献

欢迎贡献代码、报告问题或提出建议！

1. Fork 项目
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

## 📄 许可证

本项目采用 MIT 许可证 - 详见 [LICENSE](LICENSE) 文件

## 🙏 致谢

- [Anthropic Claude](https://www.anthropic.com/) - 强大的 AI 模型
- [Google Gemini](https://ai.google.dev/) - 创新的多模态 AI
- [OpenAI Codex](https://openai.com/blog/openai-codex) - 专业的代码生成
- [memex-cli](https://github.com/chaorenex1/memex-cli) - AI 后端调用工具

## 📞 联系方式

- 问题反馈: GitHub Issues

---

**从一句话需求到完整代码，Coding Workflow 让 AI 开发触手可及** 🚀
