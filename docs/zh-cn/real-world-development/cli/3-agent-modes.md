---
title: "第 3 课 - 智能体模式：Plan 和 Autopilot"
description: "使用 Plan 确定方案、使用 Autopilot 根据 issue 构建筛选功能，并在 Interactive 模式下审查结果。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

我们先为项目添加了一项小功能，但更复杂的更改需要更完善的流程。GitHub Copilot CLI 专为融入组织现有工作流程而设计，帮助我们用正确的方法构建正确的内容。本课将开启一系列课程，带你遵循典型的智能体驱动开发流程：从 issue 开始生成新功能，确保代码有效、功能行为符合预期，最终成功合并到项目中。

> [!NOTE]
> 在后续功能工作流中，你将沿用同一对话和分支。通常，不同类型的文件会使用不同分支或 PR，但这里会采用简化方式，以便专注于核心概念。

本课将介绍如何：

- 从 GitHub issue 启动新的 Copilot 对话；
- 在 Plan 模式中定义需求；
- 使用 Autopilot 模式实现新功能；
- 审查代码；
- 在转发的浏览器中手动验证功能。

继续开发此功能时，你还将更新存储库指令、自定义现有 `quality-checks` 技能、添加 MCP 验证、创建 QA 智能体并打开功能 PR。

## 场景

Tailspin Toys 的游戏目录不断扩大，访客需要按类别和发行商筛选游戏。待办 issue 描述了功能，但编码前需要就组合类别等细节达成一致。你将使用 Plan 模式解决这些问题，然后授权 Autopilot 在明确范围内实现功能。

## 背景

将 AI 编码智能体引入开发流程不会改变基本原则。事实上，这些原则反而更加重要！大多数开发者遵循类似以下的流程：

1. 从已创建的 issue 开始，了解需要完成的工作。
2. 为需要构建的内容制定计划。
3. 构建并审查代码。
4. 运行测试以验证代码。
5. 手动验证新功能。
6. 创建拉取请求 (PR)。
7. 代码通过审查且持续集成流程成功后，合并代码。

> [!NOTE]
> 具体流程会因团队和组织而异，但大多数都是以上流程的变体。

坚持这种标准方法，可以确保 AI 生成的代码满足既定要求，并经过与手写代码相同的审查流程。

## 对话模式

**对话模式**控制智能体的自主程度。按 <kbd>Shift</kbd>+<kbd>Tab</kbd> 在模式之间切换：

- **Interactive**：你与智能体协同工作。智能体提出更改建议，并等待你的输入后再继续。
- **Plan**：智能体先创建计划，但无法编辑项目文件。
- **Autopilot**：智能体自主工作，包括编写代码、运行测试并不断迭代，直至任务完成。

先从 Plan 模式开始，审查计划，然后使用 Autopilot 实现计划。

## 从 issue 开始

开始筛选功能开发前，返回 Codespace，并确认存储库和终端已准备就绪。

1. 返回 Codespace。如果它已停止，请先重新启动。
2. 确认星级评分 PR 已合并。
3. 如果终端尚未打开，请按 <kbd>Control</kbd>+<kbd>\`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>\`</kbd>（Windows/Linux）。
4. 更新 `main`，然后为筛选功能创建分支：

   ```bash
   git checkout main
   git pull --ff-only
   git checkout -b game-filters-cli
   ```

5. 启动 Copilot CLI：

   ```bash
   copilot --yolo
   ```

6. 按两次 <kbd>Tab</kbd> 打开 **Issues** 选项卡。
7. 按 <kbd>A</kbd> 显示所有 issue。
8. 使用方向键选中标题为 **Allow users to filter games by category and publisher** 的 issue。
9. 按 <kbd>C</kbd> 将 issue 添加到提示中，然后返回 **Session** 选项卡。

留意提示现在以 `#7`（或类似编号）开头。`#` 可将 GitHub issue 或拉取请求 (PR) 添加到上下文中。

## 规划筛选功能

规划能让你在将功能交给 Copilot 实现前确定方案。对于复杂任务，花时间规划总是值得的。现在切换到计划模式，让 Copilot 创建计划。

1. 按 <kbd>Shift</kbd>+<kbd>Tab</kbd> 切换到 Plan 模式。确认提示框下方的模式指示器显示 **Plan**。
2. 在上一步添加的 issue 引用后输入以下提示：

   ```plaintext
   为实现此功能制定计划。
   ```

Copilot 会开始制定计划！它会先探索项目，再确定最佳方案。

3. Copilot 可能会询问筛选功能的具体行为。根据自己的偏好回答，没有错误答案！
4. 计划完成后，按 <kbd>Control</kbd>+<kbd>E</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>E</kbd>（Windows/Linux）展开计划。
5. 上下滚动审查计划。
6. 如果计划中的任何部分不符合你的决定，请要求 Copilot 修改。

## 批准 Autopilot

计划编写并审查完成后，就可以开始实现！使用 Autopilot 让 Copilot 执行计划。

Autopilot 会让 Copilot 反复处理问题，直到它认为任务完成。

1. 选择 **Accept plan and build on autopilot (recommended)**，或当前版本中类似的选项。
2. 确认提示框下方的模式指示器显示 **Autopilot**。
3. 观察 Copilot 按计划迭代、生成代码并运行测试。

> [!NOTE]
> 批准计划后可能会立即开始实现，因此请先审查计划。如果 Copilot 报告缺少依赖项或端口冲突，应先解决环境问题，再将检查视为已完成。

## 审查并验证实现

代码生成后，和其他代码一样，必须在合并前审查。现在检查代码并运行网站，确认一切正常。

1. 按 <kbd>Shift</kbd>+<kbd>Tab</kbd> 进入 Interactive 模式。确认模式指示器不再显示 **Plan** 或 **Autopilot**。
2. 输入 `/diff`，检查筛选功能实现和测试。
3. 将结果与 issue 以及规划时的决定进行比较。
4. 审查完代码后，按 <kbd>Esc</kbd> 退出差异视图。
5. 审查项目检查的结果，并要求 Copilot 解决任何失败项。

## 体验新功能

代码看起来没问题，但能正常运行吗？像之前一样启动应用，并通过 Codespaces 转发端口在浏览器中打开网站。

1. 要求 Copilot 启动应用：

   ```plaintext
   启动应用，让我在浏览器中试用筛选功能。告诉我 URL，并让服务器继续运行。
   ```

2. Codespaces 报告端口 `4321` 可用时，选择 **Open in Browser**。
3. 尝试按类别筛选、按发行商筛选，以及计划中确定的组合方式。
4. 确认重置和无结果时的行为符合 issue 和已作出的决定。
5. 返回 Codespace，并要求 Copilot 停止它启动的开发服务器。

## 总结和后续步骤

你已使用不同的对话模式构建并审查功能。本课中，你：

- 从 GitHub issue 启动了新的 Copilot 对话；
- 在 Plan 模式中定义了需求；
- 使用 Autopilot 模式实现了新功能；
- 审查了代码；
- 在转发的浏览器中手动验证了功能。

接下来，通过[使用自定义指令][next-lesson]深入了解代码生成方式，确保代码遵循已记录的实践。

## 资源

- [GitHub Copilot CLI 中的 Autopilot][autopilot]
- [Copilot CLI 命令参考][cli-reference]

[autopilot]: https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[previous-lesson]: ../2-add-star-rating/
[next-lesson]: ../4-custom-instructions/
