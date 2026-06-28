# github-contribution-skills

[English](README.md) | [中文](README.zh-CN.md)

用于完成 GitHub 开源贡献全流程的 Codex skills，从仓库分析一直到 Pull Request 提交。

## 概览

这个仓库提供一套分阶段的 Codex skill 工作流，用于完成开源贡献：

1. 寻找真实可做的 PR 机会。
2. 为选定贡献方向设计技术方案。
3. 在本地 fork 中安全实现方案。
4. 将贡献提交为 GitHub Pull Request。

推荐总入口：

```text
/github-contribution
```

如果你已经知道要执行哪个阶段，也可以直接使用对应的子 skill。

## 安装

在你自己的 Codex 或其他能够加载本地 skill / workflow 指令的 Agent 中安装 [blackappleD/github-contribution-skills](https://github.com/blackappleD/github-contribution-skills)。

## Skills

| Skill | 用途 |
|---|---|
| `/github-contribution` | 编排完整贡献流程，并按阶段路由到对应的子 skill。 |
| `/find-contribution-prs` | 分析仓库或 fork，推荐 1-3 个现实可行的 PR 方向。 |
| `/design-pr-technical-plan` | 将选定 PR 方向转化为技术方案文档和面试叙事文档。 |
| `/implement-pr-technical-plan` | 实现已确认的技术方案，运行验证，并生成 commit 计划。 |
| `/submit-contribution-pr` | 检查目标仓库 PR 要求，准备 PR 描述，并提交 Pull Request。 |

## 工作流

```text
/github-contribution
  -> /find-contribution-prs
  -> /design-pr-technical-plan
  -> /implement-pr-technical-plan
  -> /submit-contribution-pr
```

总控入口也支持从中间阶段开始：

- 已有 PR 分析报告时，从 `/design-pr-technical-plan` 开始。
- 已有技术方案时，从 `/implement-pr-technical-plan` 开始。
- 已经完成实现时，从 `/submit-contribution-pr` 开始。

## 预期产物

| 阶段 | 产物 |
|---|---|
| 仓库分析 | 贡献分析报告，以及 Top 3 PR 推荐方向。 |
| 技术方案 | `technical-plan.md` 和 `interview-narrative.md`。 |
| 执行实现 | 代码改动、测试、验证结果和实现交付报告。 |
| 提交 PR | PR 标题、正文、base/head 分支、测试摘要和 PR 链接。 |

## 仓库结构

```text
.
+-- AGENTS.md
+-- README.md
+-- README.zh-CN.md
`-- skills/
    +-- github-contribution/
    +-- find-contribution-prs/
    +-- design-pr-technical-plan/
    +-- implement-pr-technical-plan/
    `-- submit-contribution-pr/
```

每个 skill 使用标准目录结构：

```text
skills/<skill-name>/
+-- SKILL.md
+-- agents/
|   `-- openai.yaml
`-- references/
    `-- *.md
```

## 使用示例

运行完整流程：

```text
Use /github-contribution to analyze https://github.com/owner/repo and help me complete a contribution PR.
```

只寻找贡献方向：

```text
Use /find-contribution-prs to analyze this fork and recommend 1-3 PR directions.
```

基于已有分析报告设计方案：

```text
Use /design-pr-technical-plan to turn this PR analysis report into a technical plan and interview narrative.
```

实现已确认的方案：

```text
Use /implement-pr-technical-plan to implement this technical plan and run the validation strategy.
```

提交已完成的贡献：

```text
Use /submit-contribution-pr to prepare and submit the PR for this completed change.
```
