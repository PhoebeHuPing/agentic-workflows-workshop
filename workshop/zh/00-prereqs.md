# 先决条件

开始本工作坊前，请确保您已完成以下设置。

## 所需的账户和订阅

- [ ] **GitHub 账户** 拥有有效的 Copilot 订阅（**Pro、Pro+、Business 或 Enterprise**）
- [ ] 已启用 Copilot 扩展——在您的 GitHub 账户中检查 *设置 → Copilot → 扩展*（或询问您的组织管理员）

## 所需工具

- [ ] **Git** — [安装指南](https://git-scm.com/downloads)
- [ ] **GitHub CLI (`gh`)** v2.40 或更高版本 — [安装指南](https://cli.github.com/)
- [ ] **Node.js** v18 或更高版本（由某些工作流步骤使用）— [安装指南](https://nodejs.org/)

### 验证 GitHub CLI

```bash
gh --version
```

您应该看到 `gh version 2.x.x` 或更高版本。

### 使用 GitHub 进行身份验证

如果您还没有对 CLI 进行身份验证，请运行：

```bash
gh auth login
```

按照提示操作，选择 **GitHub.com** → **HTTPS** → **使用网络浏览器登录**。

登录后，验证它是否有效：

```bash
gh auth status
```

您应该看到 `✓ 已登录到 github.com`。

## 安装代理工作流扩展

`gh aw` CLI 扩展通过其自己的设置脚本安装：

```bash
curl -sL https://raw.githubusercontent.com/github/gh-aw/main/install-gh-aw.sh | bash
```

这会将 `gh-aw` 二进制文件下载并安装到 `~/.local/share/gh/extensions/gh-aw/`。

> [!TIP]
> 要在运行之前查看脚本，请先在浏览器中打开 `https://raw.githubusercontent.com/github/gh-aw/main/install-gh-aw.sh`。

验证扩展是否可用：

```bash
gh aw version
```

> [!TIP]
> 如果 `gh aw version` 显示"unknown command"，验证 GitHub CLI 是否使用 `gh --version` 安装，然后重新运行安装脚本。

## 创建或克隆此仓库

本工作坊使用 GitHub 仓库作为所有代理工作流的工作区。

1. 在 GitHub 上 **Fork** [此仓库](https://github.com/copilot-dev-days/agentic-workflows-workshop)（点击右上角的 **Fork**），或
2. **克隆** 您的 fork 本地：

```bash
gh repo clone <your-username>/agentic-workflows-workshop
cd agentic-workflows-workshop
```

> [!NOTE]
> 所有 `gh aw` 命令必须从链接到 GitHub 远程的 Git 仓库内运行。上述命令确保是这种情况。

## 验证您的设置

运行以下检查列表以确认一切就绪：

```bash
git --version          # 应打印：git version 2.x.x
gh --version           # 应打印：gh version 2.x.x
gh auth status         # 应打印：✓ 已登录到 github.com
gh aw version          # 应打印 aw 扩展版本
```

> [!IMPORTANT]
> 如果任何检查失败，请在继续之前解决。如果需要，向工作坊协助者寻求帮助。

---

一切设置完毕后，继续 [练习 1：快速开始](./01-first-exercise.md)。
