# 蜂巢技能正式设计文档

## 1. 文档状态

- 状态：Proposed
- 类型：Architecture and Workflow Design
- 目标仓库：Coding Workflow
- 更新时间：2026-03-23

## 2. 背景

当前仓库已经具备三类核心能力载体：

1. `agents/` 中的专业角色代理
2. `commands/workflow-suite/` 中的线性工作流命令
3. `skills/` 中的能力模块

现状优势是能力丰富，现状缺口是缺少一个统一的自治调度层，导致多代理协作更多表现为“串行调用链”，而不是“有组织、有状态、有升级路径的团队系统”。

蜂巢技能的目标不是替换现有 agents，而是在现有能力之上增加一个公司化、团队化、可调度的治理层。

## 3. 设计目标

### 3.1 主要目标

1. 将现有 agents 组织为可复用的团队体系
2. 用统一任务包协议和状态协议管理团队协作
3. 为复杂任务提供中央调度、状态跟踪和决策关口
4. 在不重写现有 agents 的前提下复用当前仓库能力
5. 让用户以“CEO”身份参与关键决策，而不是直接手动串联所有代理

### 3.2 非目标

1. 不替换现有 `/ow`、`/quick_feature`、`/mult-tdd-coder` 等命令
2. 不在第一阶段引入数据库或外部持久化服务
3. 不要求每个 team lead 直接执行全部专业工作
4. 不把所有团队职责塞进一个超长单体 skill

## 4. 现状依据

蜂巢方案基于以下现有事实：

1. [docs/REPO/architecture.md](../REPO/architecture.md) 已确认仓库采用 plugin-first、约定优于注册的组织方式
2. [commands/workflow-suite/ow.md](../../commands/workflow-suite/ow.md) 已存在线性多 agent 编排模式
3. [agents/mult-coder.md](../../agents/mult-coder.md) 已具备多后端协调思路和用户关口机制
4. [agents/analysis-planner.md](../../agents/analysis-planner.md) 已具备高质量规划输出协议
5. [docs/kb/repository-knowledge-overview.md](./repository-knowledge-overview.md) 已明确“文档即行为协议”是仓库稳定模式之一

## 5. 核心判断

蜂巢能力应采用“1 个协议型 skill + 8 个 team lead agents + 2 个 workflow commands”的组合方案，而不是单一 skill。

原因如下：

1. 单 skill 无法自然承载中央调度、团队边界和多轮状态回传
2. 现有仓库已经证明 command -> agent -> handoff 的编排模式可行
3. team lead 负责组织和验收，专业 agents 负责执行，更符合当前仓库已有资产
4. 这样可以最小化对现有 agents 的侵入，避免重复建设

## 6. 蜂巢总体架构

```text
                        +----------------------+
                        |      hive-ceo        |
                        |  Central Dispatcher  |
                        +----------+-----------+
                                   |
          +------------+-----------+-----------+------------+
          |            |           |           |            |
          v            v           v           v            v
   +-------------+ +--------+ +--------+ +--------+ +-------------+
   | Strategy    | |Analysis| |Arch    | |Impl    | | Quality     |
   | Team Lead   | |Lead    | |Lead    | |Lead    | | Team Lead   |
   +-------------+ +--------+ +--------+ +--------+ +-------------+
          |            |           |           |            |
          v            v           v           v            v
   Existing skills   Existing   Existing    Existing     Existing
   and evaluators    planners   architects  coders       reviewers
          \
           \
            v
   +-------------+      +-------------+
   | Docs Lead   |      | Platform    |
   |             |      | Lead        |
   +-------------+      +-------------+
```

### 6.1 组件分层

| 层级 | 组件类型 | 作用 |
|---|---|---|
| 用户入口层 | commands | 接收任务、启动蜂巢、查询状态 |
| 调度治理层 | hive-ceo | 任务分类、派单、决策、汇总 |
| 团队管理层 | team lead agents | 组织团队工作流、控制输入输出边界 |
| 专业执行层 | existing agents and skills | 实际完成分析、设计、实现、审查、文档等任务 |
| 状态持久层 | `.hive/` | 保存状态、报告、决策和汇总结果 |

## 7. 成员注册表

说明：当前仓库没有统一的 `charactor_name` 字段。本文采用以下归一规则：

1. frontmatter 中的 `name` 作为 `agent_name`
2. 正文中的 `Your Name` 作为 `charactor_name`
3. 静态成员清单统一登记在 [../../.hive/members.yaml](../../.hive/members.yaml)
4. 规范字段名统一为 `character_name`，兼容当前分析阶段使用的 `charactor_name` 说法

| 相对路径 | agent_name | character_name | 建议归属团队 | 主要角色 |
|---|---|---|---|---|
| [agents/ai-workflow-architect.md](../../agents/ai-workflow-architect.md) | ai-workflow-architect | Nexus | CEO Office / Workflow | 工作流系统架构师 |
| [agents/analysis-planner.md](../../agents/analysis-planner.md) | analysis-planner | Chouri | Analysis | 需求与实施规划 |
| [agents/architect-designer.md](../../agents/architect-designer.md) | architect-designer | スカサハ | Architecture | 系统架构设计 |
| [agents/code-reader-analyst.md](../../agents/code-reader-analyst.md) | code-reader-analyst | Code Pathfinder | Analysis | 代码库理解与上下文提取 |
| [agents/dead-code-cleaner.md](../../agents/dead-code-cleaner.md) | dead-code-cleaner | Lupa | Quality | 死代码清理与治理 |
| [agents/documentation-sync-agent.md](../../agents/documentation-sync-agent.md) | documentation-sync-agent | Quill | Docs | 文档同步 |
| [agents/interface-analyst.md](../../agents/interface-analyst.md) | interface-analyst | Micro Contract Cartographer | Architecture | 接口契约与边界分析 |
| [agents/kubernetes-expert.md](../../agents/kubernetes-expert.md) | kubernetes-expert | Helmsman | Platform | 平台与 K8s 支持 |
| [agents/mult-analysis-planner.md](../../agents/mult-analysis-planner.md) | mult-analysis-planner | フルールドリス | Analysis | 多后端分析规划 |
| [agents/mult-coder.md](../../agents/mult-coder.md) | mult-coder | 調月リオ | Implementation | 多后端协同实现 |
| [agents/mult-tdd-coder.md](../../agents/mult-tdd-coder.md) | mult-tdd-coder | 調月リオ | Implementation | 多后端 TDD 实现 |
| [agents/plan-write.md](../../agents/plan-write.md) | plan-write | ザンニー | CEO Office / Workflow | 计划持久化 |
| [agents/prompt-style-analyzer.md](../../agents/prompt-style-analyzer.md) | prompt-style-analyzer | Mimicra | Strategy / Enablement | 提示词风格分析 |
| [agents/quality-reviewer.md](../../agents/quality-reviewer.md) | quality-reviewer | カンタレラ | Quality | 质量审查 |
| [agents/repo-analyst.md](../../agents/repo-analyst.md) | repo-analyst | repo-analyst | Docs / Analysis | 仓库宏观分析 |
| [agents/rust-tauri-app-builder.md](../../agents/rust-tauri-app-builder.md) | rust-tauri-app-builder | Forge | Platform | 桌面脚手架与交付 |
| [agents/security-checker.md](../../agents/security-checker.md) | security-checker | Yinlin | Quality | 安全审查 |
| [agents/tdd-coder.md](../../agents/tdd-coder.md) | tdd-coder | エイメス | Implementation | TDD 实施 |

## 8. 团队设计

### 8.1 团队清单

| 团队 | 新增 team lead | 复用成员 |
|---|---|---|
| CEO Office | hive-ceo | ai-workflow-architect, plan-write |
| Strategy | hive-strategy-lead | prompt-style-analyzer, tech-stack-evaluator 等 skill |
| Analysis | hive-analysis-lead | analysis-planner, code-reader-analyst, mult-analysis-planner |
| Architecture | hive-architect-lead | architect-designer, interface-analyst |
| Implementation | hive-impl-lead | tdd-coder, mult-tdd-coder, mult-coder |
| Quality | hive-quality-lead | quality-reviewer, security-checker, dead-code-cleaner |
| Docs | hive-docs-lead | documentation-sync-agent, repo-analyst |
| Platform | hive-platform-lead | kubernetes-expert, rust-tauri-app-builder |

### 8.2 团队职责边界

#### CEO Office

- 职责：任务分解、任务路由、状态管理、用户决策关口、最终汇总
- 输入：用户目标、`.hive/state.yaml`
- 输出：状态文件、决策记录、总报告
- 边界：不直接承担专业实现

#### Strategy

- 职责：方案对比、技术选型、可行性评估、成本与风险判断
- 输入：待评估问题、现有技术约束
- 输出：策略报告
- 边界：只建议，不实现

#### Analysis

- 职责：需求分析、影响面分析、计划分解、验收标准定义
- 输入：用户目标、策略结论、代码上下文
- 输出：实施计划
- 边界：只读分析，不改代码

#### Architecture

- 职责：设计组件边界、数据流、接口契约、关键架构决策
- 输入：实施计划、策略约束
- 输出：架构决策文档和接口规范
- 边界：不承担编码落地

#### Implementation

- 职责：按照计划和设计进行实现，执行 TDD 或多后端协同实现
- 输入：计划、设计、验收标准
- 输出：代码变更、测试、实现报告
- 边界：不越权修改设计范围

#### Quality

- 职责：代码质量检查、安全审查、清理建议、门禁判定
- 输入：变更集、测试结果
- 输出：质量报告和 PASS/FAIL 结论
- 边界：以报告和退回为主，不直接吞掉问题继续推进

#### Docs

- 职责：文档同步、知识沉淀、仓库级说明更新
- 输入：代码变更、设计决策、质量结论
- 输出：文档更新与摘要
- 边界：只修改文档资产

#### Platform

- 职责：部署支持、构建脚本、跨平台支持、环境编排
- 输入：构建目标、部署需求
- 输出：平台配置和部署报告
- 边界：只处理环境与交付问题

## 9. 团队工作流

### 9.1 Strategy 工作流

适用任务：技术选型、引入新后端、平台替换、成本评估

流程：

1. 接收 CEO 发出的评估任务
2. 收集现有仓库和运行约束
3. 输出方案对比表和推荐结论
4. 如果没有唯一方案，上报 CEO 请求决策

### 9.2 Analysis 工作流

适用任务：复杂功能规划、缺陷定位前置分析、变更范围评估

流程：

1. 调用代码理解成员提取上下文
2. 调用规划成员拆解实施步骤
3. 输出文件范围、依赖顺序、风险等级和验收标准
4. 如需求不清晰，上报 CEO 请求澄清

### 9.3 Architecture 工作流

适用任务：模块设计、接口设计、组件拆分、系统升级

流程：

1. 读取 Analysis 团队实施计划
2. 设计组件关系与数据流
3. 提炼接口契约和架构决策
4. 如涉及重大重构，提升至 CEO 决策

### 9.4 Implementation 工作流

适用任务：功能开发、缺陷修复、重构实现

流程：

1. 将任务拆成一个或多个 implementation wave
2. 优先使用 TDD 或多后端 TDD 流程
3. 每个 wave 完成后输出局部报告与验证结果
4. 如果设计不成立，退回 Architecture 或 CEO

### 9.5 Quality 工作流

适用任务：审查、交付门禁、安全把关、清理建议

流程：

1. 对变更集执行质量审查
2. 对安全边界执行安全审查
3. 必要时补充死代码治理
4. 输出 findings 与 PASS/FAIL 结论
5. 未通过则退回 Implementation

### 9.6 Docs 工作流

适用任务：发版说明、文档同步、架构说明更新、知识沉淀

流程：

1. 根据设计和变更判断要更新的文档范围
2. 同步 API、架构和仓库知识文档
3. 输出文档更新摘要

### 9.7 Platform 工作流

适用任务：K8s、CI/CD、部署、构建兼容问题

流程：

1. 接收构建与部署目标
2. 生成平台配置和执行建议
3. 增加健康检查与回滚说明
4. 如涉及成本或环境冲突，上报 CEO

## 10. 中央调度工作流

### 10.1 调度原则

CEO 调度器负责以下逻辑：

1. 接收用户目标
2. 将目标归类为任务类型
3. 决定应经过哪些团队
4. 将标准任务包发给对应 team lead
5. 读取团队回报并决定下一步
6. 在关键关口向用户请求决策
7. 汇总所有阶段结果

### 10.2 任务类型路由

| 任务类型 | 推荐路由 |
|---|---|
| new_feature | Strategy 可选 -> Analysis -> Architecture -> Implementation -> Quality -> Docs -> Platform 可选 |
| bugfix | Analysis -> Implementation -> Quality -> Docs 可选 |
| refactor | Analysis -> Architecture -> Implementation -> Quality |
| review | Quality |
| docs_only | Docs |
| deploy | Platform |
| evaluate | Strategy |

### 10.3 决策关口

| Gate | 触发时机 | 需要决策的内容 |
|---|---|---|
| Plan Gate | Analysis 后 | 范围、优先级、计划是否接受 |
| Design Gate | Architecture 后 | 设计方案、接口方向、重构代价 |
| Quality Gate | Quality 后 | 风险接受、修复优先级、是否继续交付 |
| Scope Gate | 任意团队发现扩 scope | 是否拆分任务或扩大目标 |
| Conflict Gate | 多团队输出矛盾 | 选择哪一个方案继续 |

## 11. 协议设计

### 11.1 标准任务包

```yaml
task_id: TASK-001
objective: "实现用户登录功能"
task_type: new_feature
requested_by: user
current_stage: analysis
inputs:
  requirements:
    - "支持邮箱密码登录"
    - "失败时有明确错误提示"
  prior_reports: []
constraints:
  - "优先复用现有模式"
  - "必须经过质量门禁"
success_criteria:
  - "登录功能可用"
  - "核心路径具备测试"
```

### 11.2 标准团队回报

```yaml
team: analysis
status: complete
summary: "已完成实施计划，识别出 4 个改动点和 2 个风险。"
artifacts:
  - path: ".hive/reports/analysis/implementation-plan.md"
    type: plan
findings:
  critical: 0
  high: 0
  medium: 2
  low: 1
  verdict: PASS
escalation: null
```

### 11.3 全局状态文件

```yaml
project: coding-workflow
objective: "实现用户登录功能"
current_stage: architecture
status: in_progress
teams:
  strategy:
    status: skipped
    report: null
  analysis:
    status: complete
    report: ".hive/reports/analysis/implementation-plan.md"
  architecture:
    status: in_progress
    report: null
  implementation:
    status: pending
    report: null
  quality:
    status: pending
    report: null
  docs:
    status: pending
    report: null
  platform:
    status: pending
    report: null
decisions: []
blockers: []
```

## 12. 仓库文件布局提案

```text
skills/
  hive/
    SKILL.md

agents/
  hive-ceo.md
  hive-strategy-lead.md
  hive-analysis-lead.md
  hive-architect-lead.md
  hive-impl-lead.md
  hive-quality-lead.md
  hive-docs-lead.md
  hive-platform-lead.md

commands/workflow-suite/
  hive.md
  hive-status.md

.hive/
  members.yaml
  state.yaml
  summary.md
  templates/
    team-lead-frontmatter.yaml
  reports/
  decisions/
```

## 13. 与现有工作流的关系

### 13.1 与 `/ow` 的关系

`/ow` 提供的是预定义的线性 agent 链路，适合中小规模、一次性编排任务。

蜂巢设计在此基础上新增三种能力：

1. 跨团队状态管理
2. 用户决策关口
3. 标准化任务包与团队报告协议

换句话说，`/ow` 是串行编排，蜂巢是带组织治理的编排系统。

### 13.2 与现有 agents 的关系

蜂巢不重写以下核心能力：

1. 规划由 `analysis-planner` 和 `mult-analysis-planner` 负责
2. 架构由 `architect-designer` 和 `interface-analyst` 负责
3. 实现由 `tdd-coder`、`mult-tdd-coder`、`mult-coder` 负责
4. 审查由 `quality-reviewer` 和 `security-checker` 负责
5. 文档由 `documentation-sync-agent` 和 `repo-analyst` 负责
6. 平台支持由 `kubernetes-expert` 和 `rust-tauri-app-builder` 负责

team lead 的责任是治理，不是替代。

## 14. 分阶段落地路径

### Phase 1: 最小闭环

新增 4 个文件：

1. `skills/hive/SKILL.md`
2. `agents/hive-ceo.md`
3. `agents/hive-analysis-lead.md`
4. `commands/workflow-suite/hive.md`

目标：跑通用户目标 -> CEO -> Analysis -> 现有实现成员 -> 现有质量成员 -> 汇总的最小路径。

### Phase 2: 核心治理补全

新增 3 个文件：

1. `agents/hive-architect-lead.md`
2. `agents/hive-impl-lead.md`
3. `agents/hive-quality-lead.md`

目标：形成完整的设计、实现、质量闭环。

### Phase 3: 运营配套补全

新增 4 个文件：

1. `agents/hive-strategy-lead.md`
2. `agents/hive-docs-lead.md`
3. `agents/hive-platform-lead.md`
4. `commands/workflow-suite/hive-status.md`

目标：形成完整公司化自治体系。

## 15. 风险与待确认项

### 15.1 主要风险

1. 当前普通 agent 仍未统一声明 frontmatter `character_name` 字段，短期需以 `## Your Name` 和 `.hive/members.yaml` 共同维护
2. team lead 的提示词如果写得过重，会与已有专业 agents 发生职责重叠
3. 如果没有明确的 stop-loss 和用户关口，蜂巢容易退化为“更复杂的串行链”
4. `.hive/` 状态文件的并发写入策略在多任务并行时需要进一步明确

### 15.2 本次确认结果

1. 已新增独立成员注册表文件 [../../.hive/members.yaml](../../.hive/members.yaml)
2. 已新增统一模板 [../../.hive/templates/team-lead-frontmatter.yaml](../../.hive/templates/team-lead-frontmatter.yaml)
3. 已在 [../../README.md](../../README.md) 中公开暴露蜂巢命令规划入口
4. 已为缺少 `Your Name` 的现有 agents 补齐人设名

## 16. 结论

蜂巢能力最合理的落地方式，不是增加一个超长单体 skill，而是在当前仓库现有 agents、commands、skills 之上增加一个“组织治理层”。

这个治理层由以下三部分构成：

1. 协议型 skill
2. 中央调度 CEO agent
3. 多个 team lead agents

如果后续需要真正实现 agent 自治，公司化模拟的关键不在于再造多少角色，而在于先把三件事固定下来：

1. 任务包协议
2. 状态机
3. 决策关口

这三者稳定之后，团队和成员数量都可以继续扩展。

## 17. 证据来源

- [README.md](../../README.md)
- [CLAUDE.md](../../CLAUDE.md)
- [docs/REPO/architecture.md](../REPO/architecture.md)
- [docs/kb/repository-knowledge-overview.md](./repository-knowledge-overview.md)
- [commands/workflow-suite/ow.md](../../commands/workflow-suite/ow.md)
- [commands/workflow-suite/mult-coding-plan.md](../../commands/workflow-suite/mult-coding-plan.md)
- [commands/workflow-suite/quick_feature.md](../../commands/workflow-suite/quick_feature.md)
- [agents/analysis-planner.md](../../agents/analysis-planner.md)
- [agents/architect-designer.md](../../agents/architect-designer.md)
- [agents/mult-coder.md](../../agents/mult-coder.md)
- [agents/mult-tdd-coder.md](../../agents/mult-tdd-coder.md)
- [agents/quality-reviewer.md](../../agents/quality-reviewer.md)
- [agents/security-checker.md](../../agents/security-checker.md)

## 18. Team Lead Frontmatter 约束模板

蜂巢 team lead 使用统一模板 [../../.hive/templates/team-lead-frontmatter.yaml](../../.hive/templates/team-lead-frontmatter.yaml)。

### 18.1 必填字段

| 字段 | 约束 |
|---|---|
| `name` | 必须采用 `hive-{team}-lead` 命名 |
| `description` | 必须明确调度场景、边界和向 `hive-ceo` 回报的职责 |
| `tools` | 必须包含 `Task`，用于调度下级成员 |
| `model` | 默认 `sonnet`，仅 CEO 或超复杂 lead 升级到 `opus` |
| `role` | 固定为 `team-lead` |
| `team` | 必须使用与 `.hive/members.yaml` 相同的团队枚举 |
| `character_name` | 必填，且应与正文中的 `## Your Name` 保持一致 |

### 18.2 设计约束

1. team lead 负责治理、拆单、验收和上报，不直接替代专业成员
2. team lead 默认不应持有 `Write` 或 `Edit`，避免越过团队边界直接改动资产
3. `team` 可选值统一为 `ceo-office`、`strategy`、`analysis`、`architecture`、`implementation`、`quality`、`docs`、`platform`
4. 未来新增 `hive-*-lead` 文件时，应先登记到 `.hive/members.yaml`，再暴露到 README 或命令文档
