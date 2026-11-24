---
title: "第 6 课 - 使用 Playwright MCP 验证功能"
description: "检查或配置 Copilot CLI 中的 Playwright MCP，并通过浏览器探索筛选功能。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

如前所述，编写代码不只是写出代码。我们还需要处理数据和外部服务，并让 Copilot 能使用更多自动化功能。这正是 MCP 服务器的用武之地。MCP 服务器让 Copilot 能够使用 CLI 内置功能以外的更多工具和服务。

本课将介绍如何：

- 了解模型上下文协议 (MCP) 及 Copilot CLI 如何使用它；
- 如果尚不可用，则添加 Playwright MCP 服务器；
- 要求智能体操控浏览器并探索筛选功能。

## 场景

单元测试和端到端测试很重要，但验证 UI 更新需要实际与 UI 交互。你希望 Copilot 能像用户一样使用正在开发的网站，进一步自动验证更改，并提高对更新符合预期的信心。

## 什么是模型上下文协议 (MCP)？

[模型上下文协议 (MCP)][mcp-blog-post] 为 AI 智能体提供了与外部工具和服务通信的方式。通过 MCP，AI 智能体可以实时与外部工具和服务通信，访问最新信息并代表你执行操作。

这些工具和资源通过 MCP 服务器访问。MCP 服务器充当 AI 智能体与外部工具和服务之间的桥梁。每个 MCP 服务器都代表 AI 智能体可以访问的一组不同工具和资源。

以下是常见的 MCP 服务器：

- **[GitHub MCP Server][github-mcp]**：提供用于管理 GitHub 存储库、issue 和拉取请求的 API。
- **[Playwright MCP Server][playwright-mcp-server]**：通过 Playwright 提供浏览器自动化功能。

此外还有许多其他 MCP 服务器。GitHub 托管了 [MCP 注册表][mcp-registry]，帮助大家发现 MCP 生态系统中的项目并参与贡献。

> [!CAUTION]
> 应像对待项目中的其他依赖项一样对待 MCP 服务器。使用前，请审查其源代码、验证发布者并考虑安全影响。

## 添加 Playwright MCP 服务器

如果 Playwright MCP 服务器尚未启用，请添加它，以便 Copilot 能像用户一样与网站交互。

> [!NOTE]
> 如果 Copilot CLI 中已经配置了 Playwright MCP，请跳过这些设置步骤，直接继续下一节。

1. 返回 Codespace。
2. 在 Copilot CLI 中输入以下命令，打开添加 MCP 服务器对话框：

   ```plaintext
   /mcp add
   ```

3. 输入名称 `playwright`，然后按 <kbd>Tab</kbd>。
4. 按 <kbd>Enter</kbd> 确认服务器类型为 **STDIO**，然后按 <kbd>Tab</kbd>。
5. 在 **Command** 对话框中粘贴以下命令：

   ```plaintext
   npx -y @playwright/mcp@latest --headless --no-sandbox
   ```

6. 按 <kbd>Control</kbd>+<kbd>S</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>S</kbd>（Windows/Linux）保存新的 MCP 服务器。
7. 按 <kbd>Esc</kbd> 退出 MCP 对话框。

## 要求 Copilot 通过 Playwright 探索功能

之前你已手动确认功能符合预期。现在使用 Copilot 中的 Playwright MCP 服务器重复验证。

1. 使用以下提示词，要求 Copilot 使用 Playwright MCP 服务器验证功能：

   ```plaintext
   启动应用，并使用 Playwright MCP 根据 issue 和我们的计划检查筛选功能。告诉我哪些正常、哪些不正常，但不要做任何更改。完成后停止你启动的服务器。
   ```

> [!NOTE]
> 通常无需指定 MCP 服务器，Copilot 会自行识别并使用。不过，既然你知道需要使用哪个服务器，明确指出来也无妨。这有助于获得更一致的结果，也能节省一些 token。

2. 观察 Copilot 在浏览器中列出的验证步骤。
3. 阅读报告，确认一切符合预期。

Copilot 会启动服务器，使用 Playwright 与网站交互，停止服务器，然后提供报告。

## 总结和后续步骤

恭喜你！你使用 Playwright MCP 服务器，通过 Copilot CLI 在真实浏览器中探索了功能。总结来说，你：

- 了解了模型上下文协议 (MCP) 及 Copilot CLI 如何使用它；
- 如果 Playwright MCP 服务器尚不可用，则添加了该服务器；
- 要求智能体操控浏览器并探索筛选功能。

接下来，你将[创建 QA 自定义智能体][next-lesson]，将技能和浏览器工具结合在一个专业角色中。

## 资源

- [MCP 是什么？为什么每个人都在谈论它？][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [向 Copilot CLI 添加 MCP 服务器][add-mcp]

[previous-lesson]: ../5-agent-skills/
[next-lesson]: ../7-qa-agent/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp
[github-mcp]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[add-mcp]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers
