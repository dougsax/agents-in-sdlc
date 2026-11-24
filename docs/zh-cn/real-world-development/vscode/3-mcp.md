---
title: "练习 3 - 使用 Playwright MCP 服务器测试功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：代理模式][previous-lesson] · [下一课：自定义智能体 →][next-lesson]

你刚刚使用代理模式构建了筛选功能。打开拉取请求前，应先在浏览器中确认功能确实正常。无需自己逐一点击应用，而是连接 **Playwright MCP 服务器**，让 Copilot 操控真实浏览器为你测试功能，然后发布分支并打开 PR。

本练习将介绍如何：

- 了解模型上下文协议 (MCP) 及 MCP 服务器如何使用新工具扩展 Copilot；
- 将 Playwright MCP 服务器添加到工作区；
- 要求 Copilot 使用它在浏览器中手动测试筛选功能；
- 发布分支，并为筛选功能创建拉取请求。

## 什么是模型上下文协议 (MCP)？

当代理模式能够访问编辑器以外的资源时，它会更加强大。Copilot 通过模型上下文协议 (MCP) 实现这一点——MCP 是智能体与外部工具和服务通信的标准方式。

![Diagram showing the inner workings of agent mode and how it interacts with context, LLM and tools — including tools contributed by MCP servers and VS Code extensions](../../../_images/ex1-mcp-diagram.png)

[模型上下文协议 (MCP)](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/) 为 AI 智能体提供了与外部工具和服务通信的方式。通过 MCP，AI 智能体可以实时与外部工具和服务通信，访问最新信息（通过资源）并代表你执行操作（通过工具）。

这些工具和资源通过 MCP 服务器访问。MCP 服务器充当 AI 智能体与外部工具和服务之间的桥梁，负责管理双方的通信。外部工具可以是现有 API，也可以是 NPM 包等本地工具。每个 MCP 服务器都代表 AI 智能体可访问的一组不同工具和资源。

以下是常见的 MCP 服务器：

- **[GitHub MCP Server](https://github.com/github/github-mcp-server)**：提供一组用于管理 GitHub 存储库的 API。AI 智能体可以创建新存储库、更新现有存储库，以及管理 issue 和拉取请求。
- **[Playwright MCP Server](https://github.com/microsoft/playwright-mcp)**：使用 Playwright 提供浏览器自动化功能。AI 智能体可以浏览网页、填写表单和选择按钮。

还有许多其他 MCP 服务器可以访问不同工具和资源。GitHub 托管了 [MCP 注册表](https://github.com/mcp)，帮助大家发现 MCP 生态系统中的项目并参与贡献。

> [!CAUTION]
> 应像对待项目中的其他依赖项一样对待 MCP 服务器。使用前，请仔细审查其源代码、验证发布者并考虑安全影响。只使用可信的 MCP 服务器，并谨慎授予其访问敏感资源或执行敏感操作的权限。

## 查看 MCP 配置

`.vscode/mcp.json` 文件用于配置 VS Code 工作区可用的 MCP 服务器。

1. 在 codespace 中打开 `.vscode/mcp.json`。
2. 你应该会看到已经配置好的 `github` 服务器：

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        }
      }
    }
    ```

`github` 条目随项目模板一起提供，因此上一练习中 Copilot 能够读取待办事项中的 issue。该条目使用[远程 GitHub MCP 服务器][remote-github-mcp-server]，无需本地安装任何内容。VS Code 通过 HTTP 连接服务器，Copilot 首次使用其中一个工具时，你需要通过 GitHub 身份验证。

## 添加 Playwright MCP 服务器

现在添加第二个服务器。[Playwright MCP 服务器][playwright-mcp-server]为 Copilot 提供可控的浏览器，正好可以用于测试功能。

1. 在 `.vscode/mcp.json` 中，在 `github` 旁边添加 `playwright` 条目，使文件内容如下：

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        },
        "playwright": {
          "command": "npx",
          "args": ["@playwright/mcp@latest", "--headless"]
        }
      }
    }
    ```

2. 保存文件。

与远程 `github` 服务器不同，`playwright` 是**本地**服务器：VS Code 会在你的计算机上运行 `npx @playwright/mcp@latest` 来启动它。`--headless` 标志让 Playwright 在没有可见窗口的情况下运行浏览器，这对于没有桌面环境的 codespace 是必要的。

> [!NOTE]
> Tailspin Toys 项目已使用 Playwright 进行端到端测试，因此通常已安装 Playwright 所需的浏览器。如果 Copilot 之后报告缺少浏览器，请让它运行 `npx playwright install chromium`，然后重试。

## 启动并信任服务器

VS Code 会**按需**启动 MCP 服务器——Copilot 首次需要使用服务器工具时，VS Code 会启动服务器，并询问你是否信任它。

1. 在 `.vscode/mcp.json` 中，`playwright` 条目上方会显示 **Start** 操作。选择它以立即启动服务器，并确认配置正确。
2. 如果 VS Code 询问你是否信任服务器，请先审查配置，然后选择信任，以便启动服务器。

手动启动是可选操作。如果跳过，VS Code 会在下一步 Copilot 首次需要服务器工具时启动服务器（并提示你信任它）。

## 测试筛选功能

[Playwright MCP 服务器][playwright-mcp-server]为 Copilot 提供可控的真实浏览器。无需亲自点击应用进行检查，智能体可以打开网页、浏览页面、应用筛选条件并读取结果，然后向你总结观察到的内容。无需离开对话，这是确认功能行为符合预期的最快方式。

在底层，Playwright MCP 服务器通过页面的[无障碍树][playwright-mcp-server]工作，而不是依赖截图。这意味着智能体会像辅助技术一样，基于带有标签的结构化元素（按钮、链接和列表项）进行推理。因此，一次快速功能检查也能顺便检查基本的无障碍访问情况。

服务器已连接且应用正在运行后，要求 Copilot 测试刚构建的筛选功能：

```text
使用 Playwright MCP 服务器打开浏览器，并访问正在运行的应用 http://localhost:4321，验证新的游戏筛选功能：

1. 打开游戏页面，并记录显示的游戏数量。
2. 应用类别筛选，并确认列表只显示该类别的游戏。
3. 清除筛选，再应用发行商筛选，并确认列表只显示该发行商的游戏。
4. 同时应用类别和发行商筛选，确认结果同时符合两个条件。

报告每一步的观察结果，并指出任何行为不符合预期的地方。
```

Copilot 会通过 Playwright MCP 服务器启动浏览器、逐步完成检查，然后汇报结果。对照 issue 中的验收标准审查报告；如果发现问题，可以提出后续问题，或要求 Copilot 在创建拉取请求前修复代码。

> [!NOTE]
> 此测试要求应用在 `http://localhost:4321` 运行。如果已停止开发服务器，请在发送提示前重新启动。Copilot 首次使用 Playwright MCP 服务器时，可能需要下载浏览器；如果报告缺少浏览器，请让它运行 `npx playwright install chromium`，然后重试。

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp

## 发布分支并创建拉取请求

确认功能正常后，就可以打开拉取请求 (PR)，让团队审查。首先发布 `filtering-vscode` 分支。

1. 在 codespace 中打开 **Source Control** 面板，审查 Copilot 所做的更改。
2. 选择 **+** 图标暂存更改。
3. 使用 **Sparkle** 按钮生成提交消息。

   ![Screenshot of the Source Control panel showing the changes made](../../../_images/ex3-source-control-changes.png)

4. 选择 **Publish**，将分支推送到存储库。

## 创建拉取请求

可以通过多种方式创建拉取请求，例如使用 github.com 或 GitHub 命令行界面 (CLI)。既然你正在使用 GitHub Copilot，不妨让它代为创建 PR！它可以找到相关 issue 并将其关联到 PR。

1. 打开 Copilot Chat 面板，选择 **New Chat** 开始新会话。
2. 在智能体下拉菜单中确认选择 **Agent**，这样 Copilot 才能使用 GitHub 工具。
3. 要求 Copilot 创建 PR：

   ```plaintext
   在存储库中查找与按类别和发行商筛选相关的 issue。为当前分支创建新的拉取请求，并将其关联到正确的 issue。
   ```

4. 按需要选择 **Continue**，允许 Copilot 执行获取信息和进行操作所需的任务。首次使用 GitHub 工具时，系统可能会提示你通过 GitHub 身份验证；按照提示完成操作。
5. 留意 Copilot 如何搜索 issue、找到正确的 issue 并创建 PR。
6. 选择 Copilot 生成的链接审查拉取请求，但**暂时不要合并**。

## 总结和后续步骤

恭喜你！本练习中，你：

- 了解了模型上下文协议 (MCP) 及 MCP 服务器如何使用新工具扩展 Copilot；
- 将 Playwright MCP 服务器添加到了工作区；
- 使用该服务器在发布前通过浏览器手动测试了筛选功能；
- 发布了分支，并为筛选功能创建了拉取请求。

你使用 MCP 服务器测试了功能，但 MCP 只是扩展 Copilot 的一种方式。接下来，[创建自定义智能体][next-lesson]，简化无障碍访问审查等专项任务。

## 资源

- [MCP 是什么？为什么每个人都在谈论它？][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [GitHub MCP Server][github-mcp-server]
- [GitHub MCP 注册表][mcp-registry]
- [VS Code 中的 MCP 服务器][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration
