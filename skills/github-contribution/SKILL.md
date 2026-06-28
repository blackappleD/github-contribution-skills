---
name: github-contribution
description: 总控型 GitHub 开源贡献工作流。用于按标准流程从仓库分析到 PR 提交完整执行：使用 find-contribution-prs 分析可贡献 PR 方向，使用 design-pr-technical-plan 设计技术方案和面试叙事，使用 implement-pr-technical-plan 执行实现和验证，使用 submit-contribution-pr 准备并提交 Pull Request。适用于用户要求“做一次开源贡献”“分析仓库并提交 PR”“按流程完成 GitHub contribution”或希望由一个总入口编排多个贡献技能。
---

# GitHub 开源贡献总控 Agent

## 角色

作为开源贡献流程的编排 agent，负责按阶段调用项目内的专用 skill，维护阶段状态，传递上一步产物，并在高风险动作前暂停确认。不要把所有子 skill 的细节复制到本 skill 中；需要进入某个阶段时，加载并遵守对应子 skill 的 `SKILL.md`。

## 子 skill

标准流程由四个子 skill 组成：

1. `/find-contribution-prs`：分析目标仓库或当前 fork，筛选 1-3 个适合贡献的 PR 方向。
2. `/design-pr-technical-plan`：基于选定 PR 方向生成技术方案文档和面试叙事文档。
3. `/implement-pr-technical-plan`：根据技术方案执行代码实现、测试和交付总结。
4. `/submit-contribution-pr`：检查目标仓库 PR 要求，整理 commit、push 分支并提交 Pull Request。

## 标准调用流程

### 0. 初始化上下文

先收集或推断：

- `REPO_URL`：目标 GitHub 仓库地址。
- `LOCAL_PATH`：本地 fork 或工作区路径。
- 用户技术栈、目标时间范围和贡献偏好。
- 是否只需要部分流程，例如只分析、不实现、不提交 PR。

若当前目录就是目标仓库，优先使用当前目录；否则根据用户提供的路径或 Git remote 推断。开始前查看 `git status --short --branch`，识别已有改动。

使用 `references/workflow-state-template.md` 维护阶段状态。可以在回复中维护状态表；如果用户要求落盘，再生成实际 Markdown 文件。

### 1. 贡献方向分析

触发条件：

- 用户还没有选定 PR 方向。
- 用户只提供仓库地址或 fork，希望找到能贡献的 PR。

执行方式：

- 加载 `/find-contribution-prs`。
- 按该 skill 要求阅读项目结构、Issue、源码架构。
- 输出贡献机会分析报告和 Top 3 贡献建议。

阶段产物：

- `contribution-analysis.md` 或等价报告内容。
- 推荐 PR 方向列表。
- 入口文件、风险、预估工作量和面试叙事角度。

阶段门槛：

- 如果用户尚未选择 PR 方向，从 Top 3 中推荐一个最稳妥方向，并等待用户确认后进入下一阶段。
- 如果用户明确授权“你来选”，选择最小侵入、最容易验证、最可能被维护者接受的方向。

### 2. 技术方案设计

触发条件：

- 已经选定 PR 方向。
- 已有贡献分析报告、Issue 结论或用户指定的实现方向。

执行方式：

- 加载 `/design-pr-technical-plan`。
- 把贡献分析报告中的项目架构、缺陷、核心文件、选定方向作为输入。
- 重新阅读相关核心文件，不只基于上一步推断。
- 生成两个 Markdown 交付物：`technical-plan.md` 和 `interview-narrative.md`。

阶段产物：

- 技术方案设计文档。
- 面试叙事文档，且必须链接技术方案文档。
- 推荐实现方案、文件级改动清单、验证策略、维护者沟通草稿。

阶段门槛：

- 若方案会修改公共 API、引入依赖、扩大范围或需要维护者先确认，暂停并建议先发 Issue/Discussion。
- 若用户要求继续实现，确认选定方案后进入下一阶段。

### 3. 执行实现

触发条件：

- 技术方案已经确认。
- 用户明确要求实现，或任务目标本身是完成可提交 PR。

执行方式：

- 加载 `/implement-pr-technical-plan`。
- 输入技术方案文档中的选定方案、问题边界、成功标准、文件清单和实现步骤。
- 先完成只读准备和基线检查，再编辑代码。
- 按接口定义、核心逻辑、集成、验证的顺序执行。

阶段产物：

- 代码改动。
- 测试或验证结果。
- 实现交付报告。
- 2-4 个 Conventional Commit 提交计划。

阶段门槛：

- 遇到新增依赖、范围扩大、破坏兼容性、复杂冲突或无法区分无关改动时，必须暂停。
- 未验证通过时，不进入提交 PR 阶段，除非用户明确要求提交 draft PR 并在描述中标注风险。

### 4. 提交 Pull Request

触发条件：

- 代码实现完成。
- 相关验证已运行或已明确说明无法运行原因。
- 用户明确要求提交 PR，或授权继续完成整个流程。

执行方式：

- 加载 `/submit-contribution-pr`。
- 优先读取目标仓库 `CONTRIBUTING.md` 和 PR 模板。
- 检查本地状态、commit 范围、remote/fork/base 分支。
- 按仓库要求或默认模板生成 PR 标题和描述。
- 在权限、分支和认证明确时 push 并创建 PR。

阶段产物：

- PR 链接。
- PR 标题和描述。
- base/head 分支。
- commit 列表。
- 测试结果和 reviewer notes。

阶段门槛：

- 如果存在无关改动、force push、rebase 冲突、权限不清、base 分支不确定或 PR 范围争议，暂停确认。

## 快速路径

根据用户已有产物跳过已完成阶段：

- 已有 PR 分析报告：从 `/design-pr-technical-plan` 开始。
- 已有技术方案：从 `/implement-pr-technical-plan` 开始。
- 已经实现完成：从 `/submit-contribution-pr` 开始。
- 用户只想要面试材料：使用 `/design-pr-technical-plan` 的面试叙事阶段，不进入实现或提交。

跳过阶段时，要说明跳过原因和使用的已有产物。

## 状态汇报

每完成一个阶段，简短汇报：

- 当前阶段。
- 已产出的文件或结论。
- 下一阶段输入是否齐全。
- 是否需要用户确认。

长流程中不要一次性承诺全部完成；按阶段推进，遇到高风险动作及时停下。

## 安全边界

- 不覆盖、不回滚用户已有改动。
- 不在未确认时提交 PR、push、force push、删除分支或改写历史。
- 不把未运行的测试写成已通过。
- 不绕过目标仓库贡献规范。
- 不把多个无关贡献塞进一个 PR。

