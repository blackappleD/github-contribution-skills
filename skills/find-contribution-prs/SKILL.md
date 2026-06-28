---
name: find-contribution-prs
description: 用于分析 GitHub 开源项目或当前 fork 中适合发起 PR 的贡献机会。适用于用户提供仓库地址、本地 fork、技术栈和目标，希望筛选 1-3 个两周内可完成、适合面试讲述的 Issue、Bugfix、架构改进、Agent 能力增强、MCP/工具集成、评估、上下文管理、streaming 或 human-in-the-loop 贡献方向。
---

# 开源贡献 PR 机会分析

## 目标

帮助用户从一个 GitHub 仓库或本地 fork 中找到 1-3 个高质量 PR 方向，并继续产出技术方案、维护者沟通草稿和面试叙事框架。优先选择用户能在两周内完成、改动范围可控、维护者更可能接受、且能体现工程判断力的贡献点。

## 输入处理

先确认这些信息，缺失时从本地仓库或用户上下文推断：

- 仓库地址：优先使用用户提供的 `REPO_URL`；否则从 `git remote -v` 推断 `origin` 和 `upstream`。
- 用户技术栈：默认使用用户给出的栈；未给出时从仓库语言、依赖和用户历史偏好中谨慎推断。
- 目标约束：默认寻找 1-3 个可在两周内完成、适合面试系统讲述的 PR 方向。
- 当前分支状态：查看 `git status --short --branch`，只读分析时不要改动用户工作区。

## 输出落盘规则

- 先确定目标开源项目根目录 `OPEN_SOURCE_ROOT`：优先使用用户提供的本地 fork 路径；若当前目录就是目标仓库，则使用当前目录；不要把安装本 skill 的仓库误当成目标仓库。
- 在输出报告前确保 `OPEN_SOURCE_ROOT/tmp/` 存在。
- 贡献机会分析报告必须写入 `OPEN_SOURCE_ROOT/tmp/contribution-analysis.md`，不能只在聊天回复中输出。
- 若本 skill 在用户选定方向后继续生成技术方案设计报告，默认写入 `OPEN_SOURCE_ROOT/tmp/technical-plan-draft.md`，后续正式方案仍交给 `/design-pr-technical-plan` 产出 `OPEN_SOURCE_ROOT/tmp/technical-plan.md`。
- 如果用户只指定文件名，仍写入 `OPEN_SOURCE_ROOT/tmp/`；只有用户明确指定其他目录时，才按用户指定目录执行。
- 聊天回复只给摘要、Top 建议和已写入文件的绝对路径。
- `tmp/` 中的分析文档默认是本地工作产物，不要纳入 commit 或 PR，除非用户明确要求。

## 工作流

### 1. 建立项目理解

先阅读这些文件，文件不存在就跳过：

- `README.md`
- `CONTRIBUTING.md`
- `CHANGELOG.md` / `HISTORY.md`
- 包管理与项目配置文件，如 `package.json`、`pyproject.toml`、`setup.py`、`Cargo.toml`
- 项目主入口文件，如 `main.py`、`app.py`、`index.ts`、`src/index.ts`

然后输出 depth=3 的目录结构，标注核心模块目录，并用 2-3 句话说明：

- 项目解决什么问题。
- 核心 Agent 循环或主执行链路是什么。
- 当前更接近哪种规划范式：ReAct、Plan-and-Execute、CoT、workflow graph、无显式规划，或其他模式。

### 2. 筛选 Issue 机会

访问仓库 Issue 列表，优先使用可用的 GitHub 工具、`gh` CLI 或网页。只推荐同时满足这些条件的 Issue：

- 状态为 open。
- 标签包含 `good first issue`、`help wanted`、`bug`、`enhancement`、`documentation` 中至少一个。
- 近 60 天内有活动，明显 stale 的 Issue 降权或剔除。
- 无 assignee，且评论中没有明确的认领表达，例如 `I'll take this`、`I'm working on this`、`assigned to me`。
- 描述清晰，有期望行为、复现步骤、验收条件或维护者认可的问题边界。
- 预计改动范围在 1-3 个核心文件内，不依赖大规模重构或维护者私有上下文。

若仓库 Issue 很少或无合适 Issue，说明原因，并把重点转向源码中的低侵入架构缺口。

### 3. 扫描架构层贡献点

围绕以下维度阅读源码。每个结论必须定位到具体文件和函数、类、接口或模块，避免泛泛描述：

1. 任务规划：是否有计划生成、子任务拆解、失败重规划、计划持久化。
2. 多 Agent 协作：拓扑结构、角色边界、通信协议、状态共享、结果聚合。
3. 上下文管理：截断、滚动窗口、摘要压缩、工具调用历史保留、跨轮存储。
4. Human-in-the-loop：高风险操作确认、checkpoint、中断恢复、人工反馈融入。
5. Agent 评估：eval 数据集、trajectory 追踪、工具调用评估、LangSmith/Arize/自定义 pipeline。
6. Tool 检索与路由：全量工具注入、按需检索、工具描述质量、MCP 接入空间。
7. Streaming 与中间状态可见性：token streaming、事件流、进度订阅、前端状态展示。

搜索时优先使用 `rg`。建议关键词包括：

- `agent`、`planner`、`plan`、`react`、`executor`、`workflow`
- `context`、`memory`、`history`、`summarize`、`truncate`
- `checkpoint`、`approval`、`human`、`interrupt`
- `eval`、`trace`、`trajectory`、`metric`
- `tool`、`mcp`、`schema`、`registry`、`router`
- `stream`、`event`、`subscribe`、`progress`

### 4. 评分与排序

给每个候选方向做简短评分，优先推荐综合分最高的 1-3 个：

- 技术栈匹配度：是否匹配 Python、TypeScript、React、MCP、LangChain.js 等用户能力。
- 两周可完成度：是否能拆成小 PR，预计改动是否少于约 300 行。
- 维护者接受概率：是否符合现有架构、Issue 讨论和项目路线。
- 面试叙事价值：是否能讲清楚背景、取舍、实现、验证和结果。
- 风险可控性：是否避开公共 API 大改、全局重构、线上凭据、生产数据或高争议方向。

### 5. 输出贡献建议

使用 `references/report-template.md` 中的“贡献机会分析报告”模板输出结果。若证据不足，明确标注“需要维护者确认”或“需要进一步验证”，不要把推断包装成事实。

完整报告必须写入 `OPEN_SOURCE_ROOT/tmp/contribution-analysis.md`；聊天回复中只摘录推荐排序、关键证据和文件路径。

### 6. 选定方向后的方案设计

当用户选中某个 PR 方向，或要求继续设计方案时，重新阅读相关核心文件，再使用 `references/report-template.md` 中的“技术方案设计报告”模板输出：

- 问题边界与成功标准。
- 2-3 个可行方案及对比矩阵。
- 推荐方案和文件级改动清单。
- 可独立验证的实现步骤。
- 手动验证、单元测试和边界测试策略。
- 发给维护者的英文评论，保持专业、具体、不超过 150 字。
- 面试叙事框架。

若在本阶段生成该设计报告，必须写入 `OPEN_SOURCE_ROOT/tmp/technical-plan-draft.md`；聊天回复中只给摘要和文件路径。

## 判断原则

- 优先小而完整的贡献，不推荐“重写架构”“全面重构”“支持所有场景”这类难以合并的方向。
- 优先维护者已经表达过需求或 Issue 中有明确信号的方向。
- 对架构改进保持克制：提出最小可用版本，并说明后续扩展路径。
- 不把“看起来高级”放在“能合并、能验证、能讲清楚”之前。
- 如果需要访问 GitHub 最新 Issue 或 PR 状态，必须获取当前信息；不要依赖过期记忆。
