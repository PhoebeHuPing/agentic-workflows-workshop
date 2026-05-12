# 练习 3 - ChatOps：情感分析斜杠命令

在本练习中，您将使用 **ChatOps 模式** 创建一个斜杠命令，在黑客新闻故事的评论上执行情感分析。团队成员将黑客新闻故事的 URL 作为 GitHub 议题上的评论粘贴，代理获取并分析评论，然后直接在线程中回复。

**预计时间**：20 分钟

## 目标

- 理解代理工作流的 ChatOps 模式
- 创建由议题评论触发的斜杠命令（`/hn-sentiment`）
- 让代理获取黑客新闻评论、运行情感分析，并内联回复
- 在真实 GitHub 议题中端到端测试命令

## 背景：ChatOps 模式

ChatOps 模式允许团队成员通过在 GitHub 议题或拉取请求评论中发布 **斜杠命令** 来触发代理工作流。该模式的工作方式如下：

1. 用户发布一条包含斜杠命令和参数的评论（例如，`/hn-sentiment https://news.ycombinator.com/item?id=12345`）。
2. GitHub 触发 `issue_comment` webhook 事件。
3. 代理工作流检测斜杠命令、提取参数、运行其逻辑，并将结果作为同一线程中的回复发布。

这很强大，因为它将上下文保留在 GitHub 内部——无需切换到单独的工具。

有关更多详细信息，请参阅 [ChatOps 模式文档](https://github.github.com/gh-aw/patterns/chat-ops/)。

## 第 1 部分 - 创建斜杠命令工作流

使用 `gh aw new` 创建工作流：

```bash
gh aw new hn-sentiment
```

当交互式会话打开时，描述您想要的内容：

```
创建一个称为 /hn-sentiment 的 ChatOps 斜杠命令。当用户在 GitHub 议题
上发布以"/hn-sentiment <url>"开头的评论时，其中 <url> 是黑客新闻故事 URL
（例如 https://news.ycombinator.com/item?id=12345），执行以下操作：
1) 从 URL 中提取黑客新闻项目 ID。
2) 从黑客新闻 API 为该故事获取最多 50 条顶级评论。
3) 对评论文本进行情感分析，将每条评论分类为正面、负面或中立。
4) 生成一个摘要，显示：整体情感（带百分比分解）、排名前 3 的最正面评论
（带摘录）和排名前 3 的最负面评论（带摘录）。
5) 用 Markdown 格式化的分析回复原始议题评论。
如果没有提供 URL 或 URL 不是有效的黑客新闻项目，则用有帮助的错误消息回复。
```

代理将配置：
- **触发器**：`issue_comment`，条件是过滤以 `/hn-sentiment` 开头的评论
- **工具**：`web-fetch` 来调用黑客新闻 API
- **网络**：允许列表中的 `hacker-news.firebaseio.com`
- **安全输出**：`add-comment` 来回复议题

## 第 2 部分 - 审查生成的工作流

打开生成的文件：

```bash
cat .github/workflows/hn-sentiment.md
```

frontmatter 将类似于：

```yaml
---
name: HN Sentiment Analysis
on:
  issue_comment:
    types: [created]
    if: startsWith(github.event.comment.body, '/hn-sentiment')
permissions:
  issues: write
  contents: read
network:
  - hacker-news.firebaseio.com
tools:
  - web-fetch
safe-outputs:
  add-comment:
    max: 1
---
```

验证事项：

1. **触发条件** — `if:` 子句是否过滤使工作流仅在有人输入 `/hn-sentiment` 时运行，而不是对每条评论运行？
2. **网络允许列表** — `hacker-news.firebaseio.com` 必须列出，以便代理可以获取故事评论。
3. **安全输出** — `add-comment` 是否声明使代理可以将分析发送回议题？
4. **提示正文** — markdown 正文是否描述了如何解析 URL、调用 API、分类情感和格式化回复？

> [!TIP]
> 您可以直接编辑 `.github/workflows/hn-sentiment.md` 的 markdown 正文（在 GitHub.com 或本地），无需重新编译。例如，您可以调整输出模板或调整摘录的格式。

## 第 3 部分 - 编译、提交并推送

如果代理没有自动编译工作流，现在编译它：

```bash
gh aw compile hn-sentiment
```

提交并推送 markdown 和锁文件：

```bash
git add .github/workflows/hn-sentiment.md .github/workflows/hn-sentiment.lock.yml
git commit -m "Add hn-sentiment ChatOps slash command"
git push
```

> [!IMPORTANT]
> `issue_comment` 触发器仅在工作流锁文件位于仓库的 **默认分支**（通常 `main`）上时触发。在测试之前，确保您推送到 `main`（或将 PR 合并到 `main`）。

## 第 4 部分 - 测试斜杠命令

1. 在您的仓库中打开任何议题（或创建一个新议题，标题为"Testing ChatOps"）。
2. 用真实的黑客新闻故事 URL 发布评论，例如：

   ```
   /hn-sentiment https://news.ycombinator.com/item?id=40942509
   ```

   > [!TIP]
   > 要找到一个好的故事来测试，打开[黑客新闻](https://news.ycombinator.com/)并选择任何有 50+ 评论的故事。从浏览器地址栏复制 URL。

3. 等待 20-60 秒。代理工作流将在后台运行。
4. 刷新议题页面。您应该看到来自代理的新评论，包含：
   - 整体情感得分（% 正面 / 负面 / 中立）
   - 顶部正面和负面评论摘录的表格或列表
   - 故事标题用于上下文

### 预期输出示例

```markdown
## 🔍 黑客新闻情感分析

**故事**：为什么我们从 Kubernetes 转移到 Nomad

| 情感 | 数量 | 百分比 |
|------|------|--------|
| 😊 正面 | 23 | 46% |
| 😐 中立  | 18 | 36% |
| 😠 负面 |  9 | 18% |

**总体：大多数正面**

### 顶部正面评论
> "这正是我们公司需要的——Nomad 的操作简单性被低估了。"

### 顶部负面评论
> "我不认为这能扩展到数百个节点之外。"
```

## 故障排除

| 问题 | 解决方案 |
|------|--------|
| 代理不回复 | 检查锁文件是否在默认分支上，触发条件是否匹配。 |
| "Item not found"错误 | 验证黑客新闻项目 ID 是否正确，故事未被删除。 |
| 空评论列表 | 某些故事还没有获取顶级评论；尝试一个有 50+ 评论的故事。 |
| 权限被拒绝 | 确保 `issues: write` 在工作流 frontmatter 中并且锁文件已重新编译。 |

## 成功标准

- [ ] `.github/workflows/hn-sentiment.md` 存在并推送到 `main`
- [ ] `.github/workflows/hn-sentiment.lock.yml` 存在并推送到 `main`
- [ ] 在 GitHub 议题中发布 `/hn-sentiment <hn-url>` 触发工作流
- [ ] 代理用情感分析分解回复
- [ ] 回复包括正面和负面评论摘录

---

完成后，继续[回顾和后续步骤](./04-review.md)。
