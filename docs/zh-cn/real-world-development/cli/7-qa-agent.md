---
title: "第 7 课 - 创建并使用 QA 智能体"
description: "创建 QA 自定义智能体，将 issue 需求、quality-checks 技能和 Playwright MCP 结合起来。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

你已使用 `quality-checks` 技能运行自动化检查，并使用 Playwright MCP 在浏览器中观察筛选体验。现在通过一个具有明确 QA 流程的自定义智能体，将这些能力结合起来。

本课将介绍如何：

- 探索自定义智能体如何与指令、技能和 MCP 工具协作；
- 创建并检查可复用的质量保证配置；
- 选择 QA 智能体，并对照筛选 issue 审查其发现。

## 场景

创建拉取请求 (PR) 前，Tailspin Toys 希望以一致的方式审查需求、代码质量、自动化检查、测试覆盖率和浏览器行为。自定义智能体可以协调 QA 流程并提供可复用的报告。

## 什么是自定义智能体？

自定义智能体是通过 Markdown 配置文件定义的 Copilot 专业版本。该文件描述智能体的用途、指令和可用工具。在本研讨会中，你将在 `.github/agents/qa.agent.md` 中定义 QA 角色，并在 Copilot CLI 中选择它。

此前使用过的自定义功能各有用途。存储库指令描述团队标准，`quality-checks` 技能封装可重复执行的检查，Playwright MCP 提供浏览器工具。QA 配置告诉 Copilot 如何使用这些能力评估需求并报告发现。它不会取代这些功能，也不需要启动另一个对话。

## 创建 QA 配置文件

打开功能 PR 前，先要求 Copilot 创建可复用的 QA 配置文件。该文件将定义 QA 执行的检查及其必须遵守的边界。

1. 返回 Codespace，并确认筛选功能的对话处于 Interactive 模式。
2. 要求默认智能体创建新的自定义智能体：

   ```plaintext
   在 .github/agents/qa.agent.md 中创建名为 QA 的自定义智能体。它应根据 issue 和已达成共识的需求检查功能、遵循存储库指令、运行 quality-checks 技能、使用 Playwright MCP 验证行为，并在测试覆盖不足时添加测试。

   要求它将每项需求报告为通过、失败或受阻，并提供支持证据。更改实现代码前必须先询问，而且不得提交更改或打开拉取请求。

   目前只创建配置文件，供我审查。
   ```

## 检查配置文件

使用新智能体前，先审查其配置文件，确认 Copilot 捕获了预期的 QA 流程和边界。这样可避免你只想验证功能时，智能体却以不完整或过宽的范围更改代码。

1. 输入 `/diff`，打开 `.github/agents/qa.agent.md`。
2. 阅读 frontmatter。`description` 为必需字段，`name` 为可选字段；添加它可以为智能体提供明确的显示名称。
3. 阅读配置文件指令，确认 QA 从需求出发、遵循存储库指令、运行 `quality-checks` 技能并使用 Playwright MCP。
4. 确认 QA 报告支持证据，在更改实现代码前先询问，并且不会提交更改或打开拉取请求。
5. 如果生成的配置文件遗漏上述任何职责或边界，请先要求默认智能体进行修订，再继续。

## 根据 issue 运行 QA

Copilot CLI 会在对话启动时加载项目智能体。创建配置文件后，恢复同一筛选功能对话并选择 QA，以便它使用上下文中已有的 issue 和规划决策。

1. 使用以下提示启用智能体：

   ```plaintext
   /agent QA
   ```

> [!NOTE]
> 你刚创建的智能体可能不会立即出现在可用智能体列表中，但它仍然存在，上面的命令会启用它。

2. 使用以下提示要求 QA 智能体审查功能：

   ```plaintext
   根据 issue 和我们计划中的决定审查筛选功能。它准备好创建 PR 了吗？
   ```

3. QA 智能体开始工作！
4. 它完成后，阅读它提供的报告。

## 总结和后续步骤

你已为工作流添加可复用的专业角色，并审查了它的工作。本课中，你：

- 探索了自定义智能体如何与指令、技能和 MCP 工具协作；
- 创建并检查了可复用的质量保证配置；
- 选择 QA 智能体，并对照筛选 issue 审查其发现。

现在已准备好实现、技能更新、QA 配置、测试和验证报告，可以进行审查。接下来，你将[创建功能 PR 并使用 Agent Merge][next-lesson]，将这些内容汇总在一起。

## 资源

- [为 Copilot CLI 创建自定义智能体][create-agents]
- [自定义智能体配置][agent-config]

[previous-lesson]: ../6-mcp-playwright/
[next-lesson]: ../8-create-pull-request/
[create-agents]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli
[agent-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
