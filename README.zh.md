<!-- l10n-sync: source-file="README.md" -->
# 代理工作流工作坊

此仓库包含 **GitHub Copilot 代理工作流** 的实践工作坊。访问发布的网站或在 `workshop/` 目录中遵循工作坊步骤。

## 开始工作坊

**要开始工作坊，请从 [workshop/README.md](./workshop/zh/README.md) 开始**

或访问[发布的工作坊网站](https://copilot-dev-days.github.io/agentic-workflows-workshop)。

## 仓库结构

```
├── docs/           # 发布的 HTML 网站（GitHub Pages）
│   ├── index.html  # 主页
│   ├── step.html   # 步骤查看器（呈现 workshop/ markdown）
│   ├── styles.css
│   ├── light-theme.css
│   └── theme-toggle.js
├── workshop/       # 工作坊内容（markdown）
│   ├── README.md              # 工作坊概览
│   ├── 00-prereqs.md          # 先决条件和工具
│   ├── 01-first-exercise.md   # 练习 1 - 快速开始（gh aw init + 日常摘要）
│   ├── 02-second-exercise.md  # 练习 2 - 黑客新闻日常摘要
│   ├── 03-chatops-sentiment.md # 练习 3 - ChatOps 情感分析
│   ├── 04-review.md           # 回顾和后续步骤
│   └── images/                # 截图和图表
├── .github/
│   ├── copilot-instructions.md
│   └── workflows/deploy.yml
├── README.md
└── LICENSE
```

## 发布

当您推送至 `main` 时，工作坊网站会自动部署到 GitHub Pages。在您的仓库设置中启用 GitHub Pages（设置 → Pages → 来源：GitHub Actions）。

## 许可证

本项目遵循 MIT 开源许可证的条款获得许可。请查阅 [LICENSE](./LICENSE) 了解完整条款。

## 支持

本项目按现状提供，并可能随时更新。如有问题，请开启一个 issue。
