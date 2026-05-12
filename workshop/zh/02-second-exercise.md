# 练习 2 - 黑客新闻日常摘要

在本练习中，您将创建一个代理工作流，该工作流自动从黑客新闻中提取与专业软件工程师相关的热门故事，并将它们作为每日 GitHub 议题发布。

**预计时间**：20 分钟

## 目标

- 为代理工作流编写更丰富、更针对的自然语言提示
- 理解代理如何从外部 API（黑客新闻）获取数据
- 检查并完善生成的工作流 markdown
- 验证输出议题有用且结构良好

## 背景

[黑客新闻 API](https://hacker-news.firebaseio.com/v0/) 是公共的，不需要身份验证。它公开了以下端点：

- `GET /v0/topstories.json` — 返回最多 500 个顶部故事 ID
- `GET /v0/item/<id>.json` — 返回单个项目（故事、评论等）的详细信息

您的代理工作流将调用这些端点、筛选结果，并将它们格式化为可读的 GitHub 议题。

## 第 1 部分 - 创建工作流

使用 `gh aw new` 创建工作流：

```bash
gh aw new hn-daily-digest
```

当交互式代理会话打开时，提供以下描述：

```
为专业开发者创建一个日常摘要工作流，参考可供大型公司的开发者
今天使用的相关顶级黑客新闻故事关于技术。每个工作日，从黑客新闻 
API (https://hacker-news.firebaseio.com/v0/topstories.json) 获取
前 30 个故事，筛选到分数超过 100 的关于软件工程、云基础设施、
AI/ML、开发者工具或分布式系统的故事。对于每个符合条件的故事，
包括：标题、URL、分数、评论数量和一句话关于为什么它对企业开发者
相关的总结。创建一个标题为"HN Digest – <date>"的 GitHub 议题，
结果格式化为 Markdown 表格。
```

代理将确认触发器（工作日计划）、所需工具（`web-fetch`、`github`）、权限（`issues: write`）和网络允许列表（HN API 域），然后生成工作流文件。

> [!TIP]
> 您也可以在 GitHub Copilot Chat 中输入 `/agent` 并选择 **agentic-workflows** 来描述工作流——代理会指导您完成会话式设置。

## 第 2 部分 - 审查并完善工作流

打开生成的工作流文件：

```bash
cat .github/workflows/hn-daily-digest.md
```

该文件有两部分：

**YAML frontmatter**（在 `---` 标记之间）— 需要重新编译的配置：

```yaml
---
name: HN Daily Digest
on:
  schedule: daily on weekdays
  workflow_dispatch:
permissions:
  issues: write
  contents: read
network:
  - hacker-news.firebaseio.com
tools:
  - web-fetch
safe-outputs:
  create-issue:
    max: 1
---
```

**Markdown 正文**（frontmatter 之后）— 您提供给代理的纯英文指令。您可以直接在 GitHub.com 或任何编辑器中编辑正文，您的更改将在下一次运行时生效，**无需重新编译**。

> [!NOTE]
> 如果您想更改触发器、工具、权限或网络规则（frontmatter），您需要重新编译：`gh aw compile hn-daily-digest`。

### 检查事项

1. **网络允许列表** — frontmatter 是否包括 `hacker-news.firebaseio.com`？代理需要网络访问权限来调用 HN API。
2. **计划** — 它是否使用模糊调度（`daily on weekdays`）而不是固定 cron？模糊调度是首选的，因为它分散负载并自动添加 `workflow_dispatch` 以进行手动运行。
3. **提示正文** — 正文是否清楚地描述了筛选标准和所需的输出格式？

如果您想调整提示，只需编辑 markdown 正文，更改将在下一次运行时生效。要更新 frontmatter（例如，添加网络域），请编辑 frontmatter 并重新编译：

```bash
gh aw compile hn-daily-digest
```

## 第 3 部分 - 提交并运行工作流

提交并推送 markdown 和锁文件：

```bash
git add .github/workflows/hn-daily-digest.md .github/workflows/hn-daily-digest.lock.yml
git commit -m "Add HN daily digest workflow"
git push
```

然后触发手动运行：

```bash
gh aw run hn-daily-digest
```

等待运行完成，然后打开 GitHub 并检查 **议题** 选项卡。

### 要检查的内容

- 议题标题包含今天的日期。
- 议题正文是一个 Markdown 表格，包含故事标题、URL、分数和评论数量。
- 故事确实与企业软件开发相关（不是一般新闻或政治）。

> [!TIP]
> 如果输出看起来不错但格式不对，编辑 `.github/workflows/hn-daily-digest.md` 的 markdown 正文来添加特定的输出模板，然后推送并重新运行——不需要重新编译。

## 成功标准

- [ ] `.github/workflows/hn-daily-digest.md` 存在于您的仓库中
- [ ] `.github/workflows/hn-daily-digest.lock.yml` 存在于您的仓库中
- [ ] 工作流 frontmatter 在网络允许列表中包括 `hacker-news.firebaseio.com`
- [ ] 工作流使用模糊调度（`daily on weekdays`）
- [ ] 创建了一个标题为 **HN Digest – \<today's date\>** 的 GitHub 议题，包含故事的 Markdown 表格

---

完成后，继续 [练习 3：ChatOps 情感分析](./03-chatops-sentiment.md)。
