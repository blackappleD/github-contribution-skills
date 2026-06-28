---
name: submit-contribution-pr
description: 用于将已完成的开源贡献提交为 GitHub Pull Request。适用于用户要求提交 PR、创建 PR、准备 PR 描述、检查目标仓库 PR 要求、按贡献规范组织 commit、push 分支、使用 gh CLI 或 GitHub 工具创建 PR，并输出 PR 链接、标题、描述、测试结果和 reviewer notes。
---

# 提交开源贡献 PR

## 目标

把本地已完成并验证过的贡献改动，整理成符合目标仓库规范的 Pull Request。优先遵守目标仓库的 PR 提交要求；如果仓库没有明确要求，使用本 skill 的默认 PR 描述模板。

## 输出落盘规则

- 先确定目标开源项目根目录 `OPEN_SOURCE_ROOT`：优先使用用户提供的本地路径；若当前目录就是目标仓库，则使用当前目录；不要把安装本 skill 的仓库误当成目标仓库。
- 在生成 PR 描述草稿前确保 `OPEN_SOURCE_ROOT/tmp/` 存在。
- PR 描述草稿必须写入 `OPEN_SOURCE_ROOT/tmp/pr-description.md`，不能只在聊天回复中输出。
- 如果目标仓库有 PR 模板，按目标仓库模板填写后仍写入 `OPEN_SOURCE_ROOT/tmp/pr-description.md`。
- 如果用户只指定文件名，仍写入 `OPEN_SOURCE_ROOT/tmp/`；只有用户明确指定其他目录时，才按用户指定目录执行。
- 聊天回复只给 PR 标题、草稿路径、测试摘要、阻塞项或已创建 PR 链接。
- `tmp/` 中的 PR 描述草稿默认是本地工作产物，不要纳入 commit 或 PR，除非用户明确要求。

## 输入要求

从用户请求、当前仓库或上下文中确认：

- 目标仓库地址和本地路径。
- fork 关系：`origin`、`upstream`、当前分支和目标 base 分支。
- 已实现的改动摘要、测试结果和提交计划。
- 关联 Issue、Discussion、技术方案文档或实现报告。
- 用户是否已经明确要求执行 push 和创建 PR。

若缺少目标仓库或分支信息，先通过 `git remote -v`、`git branch --show-current`、`gh repo view`、README 或 GitHub 页面补齐。

## 工作流

### 1. 检查仓库 PR 要求

提交前优先查找目标仓库的规范文件，存在则遵守：

- `CONTRIBUTING.md`
- `.github/PULL_REQUEST_TEMPLATE.md`
- `.github/pull_request_template.md`
- `.github/PULL_REQUEST_TEMPLATE/*.md`
- `.github/ISSUE_TEMPLATE/*`
- `docs/CONTRIBUTING.md`
- README 中的 contribution、pull request、development、testing 章节

需要确认：

- PR 标题格式。
- PR 描述模板字段。
- 是否要求关联 Issue。
- 是否要求签署 CLA、运行特定测试、更新 changelog、添加 changeset。
- 是否要求一个 PR 只做一件事、是否有 commit message 规范。

如果仓库有 PR 模板，按模板逐项填写，不要用默认模板覆盖。默认模板只在没有仓库要求时使用。

### 2. 检查本地状态

在提交前执行只读检查：

- `git status --short --branch`
- `git diff --stat`
- `git diff`
- `git log --oneline --decorate -n 5`
- 必要时检查 `git remote -v`

确认：

- 工作区只包含本次 PR 相关改动。
- 没有混入用户或其他任务的无关改动。
- 当前分支不是受保护主分支，或已经准备切到 feature/fix 分支。
- 本地分支基于合理的 upstream/base 分支。

若发现无关改动，不要擅自丢弃或回滚。需要拆分时，先说明建议并等待用户确认。

### 3. 确认验证结果

优先复用实现阶段的验证结果；若没有足够证据，运行与改动相关的最小验证：

- 单元测试、集成测试或项目推荐测试命令。
- lint、format check、type check。
- README quickstart 或最小手动验证。

如果某项无法运行，记录原因和剩余风险。不要在 PR 描述中声称未实际运行的测试已经通过。

### 4. 准备 commit

如果用户要求创建 commit，按目标仓库规范或项目 AGENTS.md 规范提交。没有明确规范时使用 Conventional Commits：

- `feat`
- `fix`
- `refactor`
- `test`
- `docs`

提交前检查 staged 文件，避免把无关文件加入 commit。不要自动执行 `git reset --hard`、`git checkout --` 或删除用户改动。

若用户未明确要求 commit，只输出建议的 commit message 和 staged 文件建议。

### 5. 准备 PR 描述

优先使用目标仓库 PR 模板。没有模板时，读取 `references/pr-description-template.md` 并生成：

- 标题：`{type}({scope}): {一句话描述}`
- Problem
- Solution
- Changes
- Testing
- Notes for Reviewer

PR 描述要客观、具体、可审查。避免夸大收益，避免写“修复了一些问题”这种空泛表达。

完整 PR 描述草稿必须写入 `OPEN_SOURCE_ROOT/tmp/pr-description.md`；聊天回复中只保留标题、摘要和文件路径。

### 6. 创建 PR

优先使用 `gh pr create`，也可以使用可用的 GitHub 工具或浏览器流程。

创建前确认：

- 分支已经 push 到正确 fork。
- base 分支正确。
- title 和 body 符合目标仓库要求。
- draft 状态是否符合用户意图。

如果用户已经明确要求“提交 PR”，且认证、远端和分支都明确，可以执行 push 和 PR 创建；如果存在不确定的 base、fork、权限、无关改动或 PR 描述争议，先停下来让用户确认。

### 7. 提交后输出

提交完成后输出：

- PR 链接。
- 标题。
- base/head 分支。
- 本次包含的 commit。
- 测试结果。
- 需要 reviewer 重点关注的点。
- 若未能创建 PR，说明阻塞原因和可复制执行的下一步命令。

## 必须暂停的情况

遇到以下情况时不要自行推进：

- 工作区存在无关改动，无法安全区分。
- 需要 force push、改写历史、rebase 复杂冲突或删除远端分支。
- 目标仓库 PR 要求不明确且会影响提交内容，例如 CLA、changeset、release note。
- GitHub 认证失败、没有 push 权限或 fork 关系不清楚。
- 需要选择多个可行 base 分支或 PR 范围。

## 质量标准

- 先查目标仓库要求，再写 PR 描述。
- PR 只包含当前贡献相关改动。
- 标题和描述让 reviewer 能快速理解问题、方案、改动和验证。
- 测试结果必须真实，不补写未运行的测试。
- 默认使用中文与用户沟通；PR 标题、描述和 commit message 通常使用英文，除非目标仓库明确使用中文。
