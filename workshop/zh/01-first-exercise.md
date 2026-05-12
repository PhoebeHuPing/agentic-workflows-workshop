# 练习 1 - 代理工作流快速开始

在本练习中，您将在您的仓库中引导 `gh aw` 工具，并创建您的第一个代理工作流：一个 **日常摘要**，总结开放的议题和拉取请求。

**预计时间**：20 分钟

## 目标

- 使用 `gh aw init` 在您的仓库中初始化代理工作流
- 理解创建的文件和配置
- 为议题和拉取请求创建并编译日常摘要工作流
- 手动触发工作流并读取输出议题

## 代理工作流如何工作

代理工作流是 **自然语言 markdown 文件**（`.github/workflows/<name>.md`），顶部有一个小 YAML frontmatter 块。frontmatter 声明诸如触发器、所需工具和权限等内容。markdown 正文是一个纯英文提示，指导 AI 代理做什么。

在工作流可以在 GitHub Actions 中运行之前，它必须 **编译** 成锁文件（`.github/workflows/<name>.lock.yml`）。您提交 `.md`（人类可读）和 `.lock.yml`（机器可读）两个文件。

## 第 1 部分 - 初始化代理工作流

从您的仓库根目录运行：

```bash
gh aw init
```

该命令为代理工作流设置您的仓库。它创建几个文件，包括：

- `.gitattributes` — 将编译的锁文件标记为生成的
- `.github/aw/github-agentic-workflows.md` — 完整的参考文档
- `.github/agents/agentic-workflows.agent.md` — 用于创建和编辑工作流的 AI 助手
- `.vscode/settings.json` 和 `.vscode/mcp.json` — 编辑器配置

> [!NOTE]
> `gh aw init` 需要对仓库的写入权限。请确保您在自己的 fork 中工作。

### 检查生成的文件

`init` 完成后，查看创建的内容：

```bash
git status
ls .github/aw/
ls .github/agents/
```

> [!TIP]
> 文件 `.github/aw/github-agentic-workflows.md` 是所有 frontmatter 选项的完整参考。当您需要检查支持的触发器、工具或权限时，请打开它。

## 第 2 部分 - 为议题和拉取请求创建日常摘要

使用 `gh aw new` 创建您的第一个工作流：

```bash
gh aw new daily-digest
```

这会打开与 `agentic-workflows` AI 代理的交互式会话。使用纯英文描述您想要什么——例如：

```
每个工作日，创建一个 GitHub 议题来总结此仓库中的所有开放议题
和拉取请求。按标签分组。包括总数、标题、作者和每个项目已
打开多长时间。将议题标题为"Daily Digest – <date>"。
```

代理会提出澄清问题（例如使用哪个触发器以及是否需要写入权限），然后为您生成工作流文件。

> [!TIP]
> 您也可以通过 GitHub Copilot Chat 提供您的描述来非交互式地运行 `gh aw new daily-digest`——打开 Copilot Chat，输入 `/agent`，然后选择 **agentic-workflows**。

### 创建的内容

代理完成后，您将拥有：

- `.github/workflows/daily-digest.md` — 带有 YAML frontmatter 和您的提示的人类可读工作流
- `.github/workflows/daily-digest.lock.yml` — 为 GitHub Actions 编译的机器可读文件

打开 markdown 文件查看代理写了什么：

```bash
cat .github/workflows/daily-digest.md
```

frontmatter 将类似于：

```yaml
---
name: Daily Digest
on:
  schedule: daily on weekdays
  workflow_dispatch:
permissions:
  issues: write
  contents: read
safe-outputs:
  create-issue:
    max: 1
---
```

正文是代理应该做什么的纯英文描述。

> [!NOTE]
> 代理工作流文件是常规 markdown——像任何其他代码一样将它们提交到版本控制。`.lock.yml` 是自动生成的，不应手动编辑。

## 第 3 部分 - 手动触发工作流

提交并推送生成的文件，然后立即触发工作流来测试它：

```bash
git add .gitattributes .github/workflows/daily-digest.md .github/workflows/daily-digest.lock.yml
git commit -m "Add daily digest workflow"
git push
```

推送后，触发手动运行：

```bash
gh aw run daily-digest
```

运行完成后（通常不到一分钟），打开 GitHub 并检查 **议题** 选项卡。您应该看到一个新议题，标题为 **Daily Digest – \<today's date\>**。

> [!NOTE]
> 如果仓库没有开放议题或 PR，摘要将说明这一点。这是预期的——工作流仍在正确工作。

## 成功标准

- [ ] `gh aw init` 已完成并在 `.github/aw/` 和 `.github/agents/` 中创建了文件
- [ ] `.github/workflows/daily-digest.md` 存在于您的仓库中
- [ ] `.github/workflows/daily-digest.lock.yml` 存在于您的仓库中
- [ ] 工作流已推送并无错误地触发
- [ ] 新的 GitHub 议题，标题为 **Daily Digest – \<today's date\>** 已在您的仓库中创建

---

完成后，继续 [练习 2：黑客新闻日常摘要](./02-second-exercise.md)。
