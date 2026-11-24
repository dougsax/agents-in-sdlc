---
title: "练习 2 - 使用 Copilot 代理模式添加功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

即使是最简单的应用更新，通常也需要修改多个文件并执行一些操作，例如运行测试。开发者的工作流程通常包括：找到所有相关文件、进行更改、运行测试、调试、找出遗漏的文件、再次更新……后续还有许多类似步骤。

Copilot 代理模式正是为此而设计的。

Copilot 代理模式能在 IDE 中更自主地工作。它的行为类似开发者：先探索现有项目结构，再执行必要的更新、运行测试等任务，并自动修复发现的问题。现在来了解如何使用代理模式为网站添加新功能。

> [!NOTE]
> 代理模式和云智能体的名称相似，但适用于不同的工作方式。代理模式在 IDE 中执行任务，支持快速反馈和交互。云智能体则像团队成员一样异步工作，通过 issue 和拉取请求与你协作。

本练习将介绍如何：

- 使用 Copilot 代理模式探索项目、查找相关文件并协调更改；
- 使用 GitHub Copilot 代理模式跨 UI 和数据层实现新功能；
- 在将代码合并到代码库之前，审查 Copilot 代理模式生成的更改和测试。

## 场景

随着游戏数量增加，你希望允许用户按类别和发行商筛选。上一练习已添加发行商辅助函数，现在将使用 Copilot 代理模式完成剩余的数据层、UI 和测试工作。

## 运行 Tailspin Toys 网站

进行任何更改前，先探索 Tailspin Toys 网站，了解它目前的功能。

这是一个以开发者为主题的桌游众筹平台。用户可以浏览游戏列表并查看详情。网站是单体 Astro 应用，会在构建时将页面呈现为静态 HTML。页面通过 Drizzle ORM 在 frontmatter 中直接查询本地 SQLite 数据库，没有单独的后端 API 或客户端 UI 框架。可复用的数据访问辅助函数放在 `src/lib/` 中；需要交互功能时，使用调用标准 DOM API 的小型、限定范围的 Astro `<script>`。

### 启动网站

项目提供了 `npm run dev` 任务，方便启动单体 Astro 开发服务器。按照以下步骤，在 GitHub Codespace 中运行：

1. 返回 codespace。上一练习结束后，你应该已切回 `main`。
2. 按 <kbd>Control</kbd>+<kbd>\`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>\`</kbd>（Windows/Linux），在 Codespace 中打开新终端窗口。
3. 创建并切换到用于筛选功能的新分支：

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 运行以下命令启动网站：

   ```bash
   npm run dev
   ```

   Astro 开发服务器就绪后，会显示类似以下的 URL 提示：

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> 如果弹出对话框，询问是否要在浏览器窗口中打开 `http://localhost:4321`，请选择 **x** 将其关闭。

5. 在终端中按住 <kbd>Command</kbd>（Mac）或 <kbd>Ctrl</kbd>（Windows/Linux），并选择 Astro 服务器地址 `http://localhost:4321`，打开网站。

> [!NOTE]
> 在 codespace 中选择终端里的 localhost URL 时，系统会自动将你重定向到 `https://<your-codespace-name>-4321.app.github.dev/`。这是通往 codespace 的专用隧道，Web 服务器正在此运行！

### 浏览网站

网站运行后，可以探索其功能。网站主要包含以下功能：

- **主页**：显示桌游列表，包括标题、图片和说明。
- **游戏详情页**：选择游戏后，会打开包含更多游戏信息的详情页，例如标题、说明、发行商和类别。

## 使用 Copilot 探索待办事项

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 codespace，打开 Copilot Chat 面板并选择 **New Chat**，开始全新对话。不同练习所需的模式和模型可能不同；每个步骤会在相关位置说明。

网站初始版本可以正常运行，但我们希望添加更多功能。先查看待办事项列表。通过模板设置项目时，系统已经自动创建了一组 issue；让 GitHub Copilot 显示这些 issue。

1. 在 Chat 视图的智能体下拉菜单中选择 **Agent**。Agent 智能体会自主规划并跨文件实现更改、运行终端命令及调用工具。

   ![Screenshot showing the agent picker in the Chat view.](../../../_images/shared-chat-mode-selector.png)

2. 从可用模型列表中选择 **Claude Sonnet 4.5**。

> [!CAUTION]
> 本研讨会作者并未偏好某个模型。编写本研讨会时使用的是 Claude Sonnet 4.5，因此将其列入说明。希望这样能让代码建议相对一致，提供良好的体验。不过，由于 LLM 具有概率性，你收到的建议可能与研讨会示例不同，这是正常且符合预期的。

> [!NOTE]
> 由于 LLM 具有概率性，Copilot 可能会使用不同的 MCP 命令，但仍应能完成任务。

3. 向 Copilot 发送以下提示，询问 GitHub 存储库中的待办事项：

   ```plaintext
   请显示我 GitHub 存储库中的待办事项，并根据对用户的帮助程度协助确定优先级。
   ```

4. 选择 **Continue**，运行列出所有 issue 的命令。
5. 查看生成的 issue 列表。

留意 Copilot 甚至会根据它认为对用户最有用的程度为事项排序。

## 查看指令文件

让智能体生成代码前，先查看将用于提供上下文的指令文件。我们将使用[用户界面 (UI) 指令][ui-instructions]，了解如何为网站添加功能。

1. 在 codespace 中打开 `.github/instructions/ui.instructions.md`。
2. 留意有关如何添加功能的整体指导，包括：
   - 架构概述；
   - 组件设计、可测试性和无障碍访问原则；
   - 指向各类文件专用指令的链接，包括：
     - Astro
     - Tailwind CSS
     - Drizzle

> [!TIP]
> 指令文件可以引用其他指令文件和项目文件。路径相对于指令文件所在位置。这样便于重复使用内容、将复杂指令拆分为较小且易于管理的部分，并提供示例和模板。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md

## 实现筛选功能

实现筛选功能至少需要对应用进行三项更改：

- 在数据层（`src/lib/`）添加或完善剩余的筛选逻辑；
- 添加或更新筛选行为测试；
- 更新游戏列表页，添加筛选 UI。

此外，合并前必须运行并通过测试。Copilot 代理模式可以为你完成这些任务！现在添加功能。

1. 可以继续当前 Copilot 对话，也可以选择 **New Chat** 开始新对话。
2. 选择 **Add Context**、**Instructions**，然后选择 **ui** 指令文件。

   ![Screenshot showing an example of selecting the UI instructions file](../../../_images/ex3-select-instructions-file.png)

3. 确认 Chat 视图的智能体下拉菜单中仍选择 **Agent**。

   ![Screenshot showing the agent picker in the Chat view.](../../../_images/shared-chat-mode-selector.png)

4. 确认模型仍选择 **Claude Sonnet 4.5**。
5. 使用以下提示，要求 Copilot 根据待办事项中的相关 issue 实现功能：

   ```plaintext
   请根据待办事项中相关 GitHub issue 的要求，为网站添加发行商和类别筛选功能。上一练习已经添加发行商辅助函数，请保留并根据需要完善该工作，同时完成其余的数据层、UI 和测试。结束前确保所有测试通过。服务器已经在运行，无需重新启动。
   ```

6. 观察 Copilot 如何先探索项目并查找与功能相关的文件。你会看到它找到数据层辅助函数、UI 和测试，然后开始修改文件并运行测试。

   ![Screenshot showing Copilot exploring the project files](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot 会执行多个任务，例如探索项目、修改文件和运行测试。根据任务和代码库的复杂程度，这可能需要几分钟。在此过程中，代码编辑器中可能会显示 **Keep** 和 **Undo** 按钮。Copilot 完成后，所有更改都会提供 **Keep** 或 **Undo** 选项，因此不必在工作进行期间选择它们。

7. 按照 Copilot 的提示，选择 **Continue** 运行测试。

   ![Screenshot showing a dialog in the Copilot Chat pane asking the user to confirm they are happy to run tests](../../../_images/ex3-agent-mode-run-tests.png)

8. 过程中可能会暂停，甚至出现测试失败，这没关系！Copilot 会在生成代码和运行测试之间反复迭代，直到完成任务且未检测到错误。

   ![Screenshot showing a complete Chat session with Copilot Agent Mode](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 检查生成的代码，查找潜在问题。

> [!CAUTION]
> 请记住，始终需要审查 Copilot 或其他 AI 工具生成的代码。

10. 返回正在运行网站的浏览器，探索新功能！
11. 确认一切正常并完成代码审查后，在 Copilot Chat 窗口中选择 **Keep**。

## 总结和后续步骤

恭喜你！本练习介绍了如何使用 GitHub Copilot 代理模式为 Tailspin Toys 网站添加新功能。我们了解了如何：

- 使用 GitHub Copilot 代理模式跨 UI 和数据层实现新功能；
- 使用 Copilot 代理模式探索项目、查找相关文件并协调更改；
- 在将代码合并到代码库之前，审查 Copilot 代理模式生成的更改和测试。

现在使用 [Playwright MCP 服务器测试功能][next-lesson]，并为其创建拉取请求。

### 额外探索练习 — 实现分页

随着游戏列表不断增加，网站需要支持分页。使用本练习中学到的技能，提示 Copilot 为网站添加分页。编写代码时可考虑以下事项：

- 遵循现有最佳实践，包括使用已有指令文件；
- 考虑分页方式，例如是否允许用户选择每页大小，还是使用固定值；
- 编写提示时，提供足够指导，让 Copilot 按照你的设想实现功能；
- 可能需要与 GitHub Copilot 多次迭代，请求更改并补充上下文。这是使用 Copilot 的正常流程！

## 资源

- [Copilot ask、edit 和 agent 模式：功能及适用场景][choose-mode]
- [VS Code 中的代理模式][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
