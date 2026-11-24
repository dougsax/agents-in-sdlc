---
title: "第 4 课 - 使用自定义指令引导 Copilot"
description: "查看存储库指令、添加文档标准，并将其应用于筛选功能代码。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

使用生成式 AI 时，上下文至关重要。如果任务需要以特定方式完成，就应向 Copilot 提供相应指导。[指令文件][instruction-files]不仅说明需要什么代码，还说明代码应如何组织。筛选功能已经构建完成，现在来了解 Copilot 使用的指令、添加文档标准，并将其应用于代码。

本课将介绍如何：

- 探索存储库指令和按路径指定的指令文件如何传递给智能体；
- 更新指令文件以确保遵循编码标准；
- 查看指令文件对代码的影响。

## 场景

像所有优秀的软件开发团队一样，Tailspin Toys 制定了一套开发实践指南和要求，包括：

- 注释应说明意图和不明显的决策，而不是复述代码；
- `db/` 和 `src/lib/` 中导出的函数应使用 TSDoc/JSDoc 记录用途、参数和返回值；如果存在可注入的 `db` 参数，也应记录；
- 可复用的 Astro 组件应记录其 `Props` 契约，并在相关代码变化时同步更新注释；
- 应保留现有格式和 lint 指导。

通过指令文件，你可以确保 Copilot 获得正确的信息，并按照这些实践完成任务。

## 指令文件

自定义指令可向 Copilot 提供上下文和偏好，帮助它更好地理解你的编码风格和要求。你可以指定偏好的编码约定、库，甚至希望代码包含的注释类型。可以为整个存储库创建指令，也可以针对特定文件类型创建指令，为任务提供上下文。

指令文件有两种：

- `.github/copilot-instructions.md` 是一个指令文件，会随存储库中的**每个**请求一起发送给 Copilot。此文件应包含项目级信息，即与存储库中大多数请求相关的信息。
- `.github/instructions/*.instructions.md` 可针对特定语言、文件类型或任务提供指导。

自定义指令可向 Copilot 提供上下文和偏好，帮助它更好地理解你的编码风格和要求。你可以指定偏好的编码约定、库，甚至希望代码包含的注释类型。可以为整个存储库创建指令，也可以针对特定文件类型创建指令，为任务提供上下文。

指令文件有两种：

- `.github/copilot-instructions.md`：每次针对存储库的请求都会发送给 Copilot 的单个指令文件。此文件应包含项目级信息，即与存储库中大多数请求相关的信息。
- `.github/instructions/*.instructions.md`：可针对特定语言、文件类型或任务提供指导。

> [!NOTE]
> 其他指令格式及支持情况因工作环境而异。依赖某种格式前，请查阅[自定义指令支持参考][custom-instructions-support]。

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## 浏览本项目中的自定义指令文件

初始项目已包含一组指令文件。更改之前，先浏览现有内容并了解其影响。

1. 返回 Codespace。
2. 在 Codespaces 编辑器（而非终端）中打开 `.github/copilot-instructions.md`。
3. 浏览文件，留意项目的简要说明和编码指导。此文件中的指令适用于你在此存储库中与 Copilot 的所有交互。
4. 打开 `.github/instructions` 文件夹并浏览其中的文件。你会看到针对 Astro 文件、Drizzle 数据层、测试等内容的指令。
5. 打开 `.github/instructions/unit-tests.instructions.md`。留意顶部的 `applyTo` 字段，它设置了一个 glob，用于指定指令适用的文件。
6. 打开 `.github/instructions/drizzle.instructions.md`，留意指向其他指令文件和项目现有文件的链接。这样可以将较大的指令集拆分成较小、可复用的文件，并为 Copilot 提供示例。

## 更新指令文件以符合团队指南

现有文件是良好的起点，但仍有缺漏。现在修改核心 `copilot-instructions.md` 文件，确保为新生成的 TypeScript 添加 TSDoc 注释。

1. 在 `.github/copilot-instructions.md` 中，找到标题为 **Code formatting guidance** 的部分，通常在第 35 行附近。
2. 将以下内容添加为该部分的最后一个列表项：

   ```markdown
   - 所有新编写的 TypeScript 都应包含 TSDoc 注释。
   ```

文件会自动保存！

## 使用更新后的指导

Copilot CLI 会在对话启动时加载存储库指令。保存更新后的指令后，退出并恢复筛选功能对话，让 Copilot 在保留功能上下文的情况下读取新指令。

1. 如果筛选功能对话仍处于打开状态，请输入 `/exit` 关闭。Copilot CLI 会自动保存对话。
2. 在存储库根目录的 `game-filters-cli` 分支中恢复已保存的对话：

   ```bash
   copilot --resume
   ```

   如果系统提示，请选择筛选功能对话，并确认当前检出的是 `game-filters-cli` 分支。
3. 要求 Copilot 应用更新后的指导：

   ```plaintext
   我们刚刚更新了指令和代码指导。请更新你生成的代码，使其符合这些指导。
   ```

4. 输入 `/diff`，查看更改的 TypeScript 文件。留意新生成的 TSDoc 注释，并确认它们准确说明了代码。

## 总结和后续步骤

你了解了 Copilot CLI 如何从指令文件获取上下文，并将新标准应用于功能。具体而言，你：

- 探索了存储库指令和按路径指定的指令文件如何传递给智能体；
- 更新了指令文件以确保遵循编码标准；
- 查看了指令文件对代码的影响。

接下来，[自定义并运行可复用的 quality-checks 技能][next-lesson]，确保始终如一地运行 lint 和测试。

## 资源

- [为 Copilot CLI 添加自定义指令][instruction-files]
- [自定义指令支持][custom-instructions-support]
- [创建自定义指令的最佳实践][instructions-best-practices]
- [Awesome Copilot — 指令文件和其他资源集合][awesome-copilot]

[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
