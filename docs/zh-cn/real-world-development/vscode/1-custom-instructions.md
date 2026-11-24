---
title: "练习 1 - 自定义指令（VS Code）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：代理模式 →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务需要以特定方式完成，或 Copilot 需要了解一些背景信息，就要确保它可以访问这些上下文。可以通过多种方式向 Copilot 提供特定上下文，其中一种重要方式是使用[指令文件][instruction-files]，指导 Copilot 如何生成代码。

本练习将介绍如何：

- 通过存储库自定义指令和按路径指定的指令文件，将项目上下文、编码指南和文档标准传递给 Copilot；
- 在保留现有指令的情况下，使用当前指令生成筛选功能的第一个数据切片（发行商辅助函数）；
- 在 `.github/copilot-instructions.md` 中添加一项适用于整个存储库的新标准；
- 重新运行相同提示，观察生成的代码如何采用新标准；
- 将指令更新和筛选功能切片提交到 `main`，让 Copilot 在下一练习中使用更新后的指导。

> [!CAUTION]
> 生成的代码可能不符合你设定的某些标准。Copilot 的输出具有非确定性。本练习的重点是观察更新指令前后行为的*整体趋势*，而不是要求输出逐字一致。

## 指令文件

### 场景

像所有优秀的软件开发团队一样，Tailspin Toys 制定了一套开发实践指南和要求，包括：

- 数据层始终需要单元测试；
- UI 应采用暗色模式并具有现代感；
- 应使用 TSDoc 文档注释为代码编写文档；
- 每个文件开头都应添加一段注释，说明文件的用途。

通过使用指令文件，你可以确保 Copilot 获得正确的信息，并按照这些实践完成任务。

### 自定义指令

自定义指令可向 Copilot 提供上下文和偏好，帮助它更好地理解你的编码风格和要求。你可以指定偏好的编码约定、库，甚至希望代码包含的注释类型。可以为整个存储库创建指令，也可以针对特定文件类型创建指令，为任务提供上下文。

指令文件有两种：

- `.github/copilot-instructions.md` 是一个指令文件，会随存储库中的**每个**请求一起发送给 Copilot。此文件应包含项目级信息，即与发送给 Copilot 的大多数聊天或 CLI 请求相关的上下文，例如所用技术栈、项目概述、最佳实践和全局指导。
- `.github/instructions/*.instructions.md` 可针对特定任务或文件类型创建。你可以为特定语言（如 TypeScript 或 Astro）或任务（如创建 UI 组件或一组新单元测试）提供指南。

> [!NOTE]
> 在 IDE 中工作时，指令文件仅用于 Copilot Chat 的代码生成，不用于代码补全或下一步编辑建议。
>
> Copilot Chat、Copilot CLI 和 Copilot 云智能体在生成代码时，都会使用存储库级指令文件以及带有 `applyTo` frontmatter 的 `*.instructions.md` 文件。
>
> Copilot 还[支持其他标准的指令文件][custom-instructions-support]，包括 AGENTS.md 和 CLAUDE.md 文件。

### 管理指令文件的最佳实践

完整介绍如何创建指令文件不在本练习范围内。不过，示例项目展示了一种有代表性的方法。总体而言：

- 将 `copilot-instructions.md` 限定为项目级指导，例如项目说明、项目结构和全局编码标准。
- 使用 `*.instructions.md` 为文件类型（例如单元测试、Astro 组件、数据层）或特定任务提供指导。
- 使用自然语言，确保指导清晰，并提供代码的正反示例。

创建指令文件没有唯一正确的方法，使用 AI 也一样。通过试验，你会逐渐找到适合项目和场景的方法。

> [!TIP]
> 每个使用 GitHub Copilot 的项目都应该有一套完善的指令文件。浏览本项目中的文件时，你会发现其中有针对多种任务的指令，包括 [UI 更新][ui-instructions]和 [Astro][astro-instructions]。
>
> Copilot 也可以帮助生成指令文件。不同工作界面的操作方式各不相同，例如 VS Code 中的 **Configure Chat → Generate Agent Instructions**，或 Copilot CLI 中的 `/init`。所在工作界面的课程会在相关位置介绍这些操作。
>
> 想查找模板或起点？可以浏览 [awesome-copilot][awesome-copilot]，其中收集了指令文件、自定义智能体和其他资源。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## 浏览本项目中的自定义指令文件

花点时间阅读此存储库自带的指令文件，其中包括一个核心 `copilot-instructions.md` 文件和一组用于不同任务的 `*.instructions.md` 文件。你可以在编辑器或 GitHub Web 界面中打开这些文件。

1. 打开 `.github/copilot-instructions.md`。
2. 浏览文件，留意项目的简要说明以及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。在 **Code standards** 下，还能看到嵌套的 **GitHub Actions Workflows** 指导。这些内容适用于你与 Copilot 的任何交互。
3. 打开 `.github/instructions` 文件夹并浏览其中的内容。你会看到针对 Astro 文件、Drizzle 数据层、测试等内容的指令。
4. 打开 `.github/instructions/unit-tests.instructions.md`。留意顶部的 `applyTo` 字段，它设置了一个相对于存储库根目录的 glob，用于指定指令适用的文件。此处匹配任何 TypeScript 测试文件，例如符合 `**/*.test.ts` 的文件。
5. 留意本项目中专门针对单元测试的指令。
6. 最后，打开 `.github/instructions/drizzle.instructions.md` 并滚动到文件底部。留意其中指向其他指令文件（如 `unit-tests.instructions.md`）和项目现有文件的链接。这样可以将较大的指令集拆分成更小、可复用的文件，并为 Copilot 提供代码生成示例。链接路径相对于指令文件所在位置，而不是存储库根目录。

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分记录了项目的编码标准，但目前尚未要求为代码添加文档。接下来，你会添加 TSDoc 文档注释和文件注释头的规则。

## 为更改创建分支

按照最佳实践，为更改创建一个分支。

1. 返回上一练习中使用的 codespace。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开新终端。
3. 创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions
   ```

## 更新指令前先使用 Copilot Chat

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 codespace，打开 Copilot Chat 面板并选择 **New Chat**，开始全新对话。不同练习所需的模式和模型可能不同；每个步骤会在相关位置说明。

要查看自定义指令的影响，先在当前指令文件不变的情况下发送提示。稍后更新指令后，再发送相同提示并比较结果。

1. 关闭之前练习中打开的编辑器标签页，让 Copilot 只获取你想要的上下文。
2. 打开 `src/lib/publishers.ts`，让 Copilot 知道辅助函数应放在哪里。
3. 在 Chat 视图的智能体下拉菜单中选择 **Agent**，让 Copilot 可以应用文件更改。

   ![Screenshot showing the agent picker in the Chat view.](../../../_images/shared-chat-mode-selector.png)

4. 发送以下提示：

   ```plaintext
   创建或更新 src/lib/publishers.ts，添加一个数据访问辅助函数，返回所有发行商的名称和 ID。应用文件更改。
   ```

5. Copilot 会探索项目并应用代码更新，通常会同时修改辅助函数文件和测试。
6. 留意建议的辅助函数是一个带类型的函数，它将 `db` 客户端作为第一个参数，并返回类型化的发行商数组。这来自 `.github/instructions/drizzle.instructions.md` 中的数据层约定，该文件适用于 `src/lib/*.ts`。
7. 注意建议的代码**缺少** TSDoc 文档注释和文件级注释头。

> [!CAUTION]
> Copilot 的输出具有非确定性，因此即使没有收到指示，它也可能添加文档注释。如果发生这种情况也没关系，本练习的重点仍是观察更新指令后的*一致性*改进。

## 添加存储库级标准

如前所述，`.github/copilot-instructions.md` 用于向 Copilot 提供项目级信息。接下来添加项目编码标准，以改进代码建议。

1. 重新打开 `.github/copilot-instructions.md`。
2. 找到 **Code formatting requirements** 部分，通常在第 27 行附近。注意该部分记录了项目的编码标准，但还没有规定如何为代码添加文档，这也是生成的辅助函数没有文档注释的原因。
3. 在现有标准下方添加以下 Markdown 内容，要求 Copilot 添加文件注释头和 TSDoc 文档注释：

   ```markdown
   - 每个导出函数都应包含 TSDoc 注释，说明用途、参数和返回值。
   - 在导入语句或任何代码之前，为文件添加说明其用途的注释块。
   ```

4. 保存 `copilot-instructions.md`。

> [!TIP]
> 如上一课所示，可以在存储库级别（`.github/copilot-instructions.md`）创建指令，为整个项目提供指导；也可以创建 `*.instructions.md`，为特定语言、文件类型或任务提供指导。你刚添加的文档注释规则属于项目级标准，因此适合放在存储库级文件中。

## 重新运行提示并观察变化

1. 返回 Copilot Chat，选择 **New Chat** 清除对话内容。
2. 再次打开 `src/lib/publishers.ts`，让 Copilot 关注正确的文件。
3. 发送之前的**同一条提示**：

   ```plaintext
   创建或更新 src/lib/publishers.ts，添加一个数据访问辅助函数，返回所有发行商的名称和 ID。应用文件更改。
   ```

4. 注意现在建议的文件开头会包含类似以下的注释块：

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 注意建议的函数现在包含类似以下的 TSDoc 注释：

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

你刚刚引导 Copilot 遵循新的项目标准，并将其应用到真实代码中；下一练习会在此基础上继续。

## 提交指令并推送分支

指令文件与存储库中的其他资源一样，使用相同的源代码管理流程。现在提交并推送分支。

1. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开新终端窗口。
2. 在终端中运行以下命令，确认指令和辅助函数更改已保存：

   ```bash
   git status
   ```

3. 在终端中暂存并提交指令更新，以及 Copilot 为辅助函数基础工作修改的所有文件：

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # 如果 git status 显示还有其他支持性更改（例如测试），也要添加这些文件。
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. 在终端中将分支推送到存储库：

   ```bash
   git push -u origin custom-instructions
   ```

## 创建并合并拉取请求

分支推送完成后，创建拉取请求，并将其关联到待办事项中的文档标准 issue。可以手动操作，不过 Copilot 可以使用已连接到项目的 GitHub 工具代为完成。让 Copilot 查找 issue、创建用于关闭该 issue 的 PR，然后合并 PR。

> [!NOTE]
> 这是第一次让 Copilot 代表你操作 GitHub。项目模板已将 GitHub 工具连接到工作区，因此无需额外设置。Copilot 首次使用其中一个工具时，VS Code 可能会提示你登录 GitHub。按照提示完成登录。后续练习会介绍这种连接方式（模型上下文协议）。

1. 在 codespace 中打开 Copilot Chat。
2. 按 <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd>（Windows/Linux）打开 Copilot Chat 视图，并确保在智能体选择器中选择 **Agent**。
3. 要求 Copilot 查找与更新指令文件相关的 issue，并从当前分支创建 PR，说明指令更新和新的发行商辅助函数基础工作：

   ```plaintext
   查找与更新指令文件相关的 issue。从当前分支创建 PR，指出该 PR 会关闭此 issue，并说明我们还为即将开展的筛选工作添加了发行商辅助函数基础。
   ```

4. Copilot 会开始查找 issue 并创建 PR。
5. 系统提示你**允许** Copilot 代表你执行 GitHub 操作时，请先审查命令，再酌情选择 **Allow**。
6. 创建 PR 后，使用以下提示要求 Copilot 合并 PR，并将分支切回 main：

   ```plaintext
   将 PR 合并到 main。然后在本地切回 main，并拉取最新代码以保持同步。
   ```

7. 系统提示你**允许** Copilot 代表你执行 GitHub 操作和运行 shell 命令时，请先审查命令，再酌情选择 **Allow**。

现在，你已借助 GitHub Copilot 创建并合并拉取请求！

## 总结和后续步骤

你了解了 Copilot 如何从项目指令文件获取上下文，然后在 VS Code 中使用 Copilot Chat：

- 发送代码生成提示，并观察 Copilot 如何遵循*现有*指令；
- 在 `.github/copilot-instructions.md` 中添加新的存储库级标准；
- 重新发送相同提示，观察建议的代码如何采用新标准；
- 将指令更新和辅助函数基础工作提交到 `main`，以便下一练习继续使用。

接下来，在[代理模式][next-lesson]中运用这些指令，让 Copilot 在整个代码库中添加新功能。

## 资源

- [用于自定义 GitHub Copilot 的指令文件][instruction-files]
- [创建自定义指令的最佳实践][instructions-best-practices]
- [编写更好的 Copilot 自定义指令的 5 个技巧][copilot-instructions-five-tips]
- [GitHub Copilot 的个人自定义指令][personal-instructions]
- [Awesome Copilot — 指令文件和其他资源集合][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
