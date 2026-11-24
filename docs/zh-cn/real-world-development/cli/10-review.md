---
title: "第 10 课 - 总结和后续步骤"
description: "回顾 Copilot CLI 工作流、两个拉取请求、可复用的自定义项和后续资源。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

你使用 GitHub Copilot CLI 完成了 Tailspin Toys 的连续工作流。你：

- 准备了 Codespace、安装了 Copilot CLI、探索了项目，并找到了预置的筛选功能 issue；
- 添加了星级评分，在转发的浏览器中审查结果，并手动合并了第一个拉取请求 (PR)；
- 从筛选功能 issue 开始，在 Plan 模式中确定方案、在 Autopilot 模式中构建功能，并在 Interactive 模式中审查；
- 使用自定义指令引导智能体，随后自定义现有 `quality-checks` 技能，并使用它运行单元测试、lint 和类型检查；
- 添加 Playwright 模型上下文协议 (MCP) 服务器，并用它在真实浏览器中探索筛选功能；
- 创建并选择质量保证 (QA) 自定义智能体，评估需求、覆盖率、技能结果和浏览器证据；
- 审查完整的筛选功能更改，并授权 Agent Merge 合并筛选 PR；
- 探索用于管理上下文、模型和会话分享的斜杠命令，以及可选的云端委派。

## 你交付的内容

本研讨会包含两个 PR 里程碑，每个里程碑都从更新后的 `main` 分支创建独立分支：

1. **星级评分：**在游戏卡片上显示现有的 `starRating` 和明确的未评分状态。
2. **筛选功能和质量工作流：**实现筛选功能、更新指令并将其应用于功能、自定义 `quality-checks` 报告、创建 QA 配置文件并添加相关测试。

从规划筛选功能到打开 PR，你始终使用同一对话和分支。为简化研讨会，我们将这些工作合并到一个 PR 中。

## 不同类型的验证

你通过多种方式检查了代码：自动化测试、亲自在浏览器中检查，以及通过 MCP 使用 Copilot 探索浏览器。`quality-checks` 技能运行了单元测试、lint 和类型检查，并按新格式报告结果。QA 综合这些结果，在创建 PR 前审查了需求和测试覆盖率。

新增的测试应解决实际缺口；如果 QA 检查后确认无需添加测试，也可能是正确结果。授权合并前，请审查代码和证据；更改后应重新检查受影响的证据。

## 最佳实践

提供给 Copilot 的上下文和工具会影响它的工作。在本研讨会中，你更新了指令、自定义了技能、创建了 QA 配置文件，并配置了 MCP 服务器。可以在不同对话中复用这些自定义设置，并随团队需求变化进行调整。指令设定标准，技能描述可重复执行的任务，自定义智能体定义专用角色，MCP 服务器连接外部工具。请审查实际更改和工具结果，而不只是智能体的总结。

根据任务选择**模式和模型**。使用 **Plan** 在构建前确定方案，使用 **Interactive** 密切参与聚焦的更改，使用 **Autopilot** 处理范围明确的任务。常规编辑可选择更快的模型，复杂工作则选择能力更强的模型。

上下文与基础设施同样重要。清晰说明要构建*什么*、*为什么*构建以及*如何*构建，会显著影响输出结果。

## 进一步探索

你已了解核心工作流。还可以探索以下 Copilot CLI 功能：

- `/review`：让代码审查智能体分析更改；
- `/rubber-duck`：讨论问题并获得另一种观点；
- `/fleet`：并行协调独立子任务；
- `/worktree`：隔离单独任务；
- `/delegate`：将任务发送给 Copilot 云智能体。

## 后续步骤

持续使用工具是提升技能的最佳方式！用它编写生产代码、兴趣项目，或构建你多年来一直想做却没来得及动手的小应用。与团队分享经验，也向团队成员学习。别忘了继续探索文档。

如果想继续从终端扩展 Tailspin Toys，可以接着学习可选的 [Foundry Backer Concierge 系列][foundry]。若想比较其他工作环境，可以探索 [VS Code 研讨会][vscode]、[GitHub Copilot app 研讨会][app]或 [Copilot 云智能体研讨会][cloud]。

## 资源

- [关于 GitHub Copilot CLI][about-cli]
- [Copilot CLI 命令参考][cli-reference]
- [自定义 Copilot CLI][customize-cli]
- [使用 Copilot CLI 管理拉取请求][manage-prs]

[previous-lesson]: ../9-cli-power-tools/
[foundry]: ../8-foundry-agent/
[vscode]: ../../vscode/
[app]: ../../app/
[cloud]: ../../cloud/
[about-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[customize-cli]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
