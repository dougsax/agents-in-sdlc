---
title: "练习 4 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

在前两项练习中，你让 Copilot 云智能体分别处理了三项任务，目标是改善用户体验并添加功能。云智能体虽然可以异步自主工作，但监控这些任务仍然很重要。

你可以使用多种工具管理分配给云智能体的任务，包括 GitHub.com 上的[智能体页面][agents-page]。在这个任务控制中心，你可以查看所有有未关闭拉取请求（PR）的智能体任务，了解已执行的操作，甚至可以指导正在进行的会话。

在本课中，你将：

- 浏览智能体页面，监控云智能体任务；
- 指导正在进行的会话，要求添加其他功能。

## 场景

将任务分配给智能体以创建高对比度模式后，团队意识到也可以顺便添加浅色模式。网站正在更新样式并添加切换功能，因此增加浅色模式似乎很合理。现在，你要指导智能体的工作，确保它同时添加浅色模式和高对比度模式。

## 查看 Copilot 云智能体任务

查看当前分配给 Copilot 云智能体的所有任务的状态。

1. 打开 [Copilot 智能体页面](https://github.com/copilot/agents)。
2. 留意主窗格和左侧窗格中的任务列表。你应该能看到分配给 Copilot 的任务，包括：
   - 更新代码库文档；
   - 为修改游戏生成 API；
   - 为网站添加高对比度模式。
3. 选择一个正在运行的任务，查看 Copilot 已执行的操作，例如：
   - 从存储库检出代码；
   - 为 Copilot 创建工作环境；
   - 设置 MCP 服务器；
   - 执行完成分配任务所需的各种操作。

> [!NOTE]
> 具体显示的步骤会因 Copilot 的工作状态和执行方案而异。

4. 同时留意右侧的拉取请求（PR）窗格。你可以在此查看 PR 和更改的文件，以便进一步监控。

## 指导云智能体

现在你已了解正在进行的任务，可以在智能体处理高对比度模式时，要求 Copilot 同时添加浅色模式。

1. 选择负责添加高对比度模式的会话。具体标题会因 Copilot 采用的名称和当前工作状态而异。

    ![Accessibility session in mission control](../../../_images/ex6-accessibility-session.png)

2. 观察会话几分钟，直到会话显示已完成设置并开始工作。出现类似下方的消息时，就表示已开始工作。
3. 在 **Steer active session while Copilot is working** 对话框中输入以下提示：

    ```plaintext
    添加高对比度模式时，也请添加浅色模式。还应提供一个用于切换模式的开关，让用户选择所需的显示模式。
    ```

    ![Screenshot of the cloud agent task in the agents page with the steer active session while copilot is working dialog highlighted](../../../_images/ex6-steer-cloud-agent-task.png)

4. 按 <kbd>Enter</kbd> 发送提示。
5. 留意 Copilot 如何确认提示并将其纳入工作流程。

## 等待 Copilot 完成工作

与之前一样，Copilot 会开始处理更新后的任务！完成当前步骤后，它会将新请求纳入工作流程。

和之前一样，这需要几分钟。此时可以暂停一下，回顾目前学到和探索的内容。

## 总结和后续步骤

本课介绍了 Copilot 智能体页面，这是监控和指导 GitHub Copilot 云智能体任务的中心。你可以在此跟踪所有正在进行和已完成的任务、审查智能体执行的工作，甚至重定向进行中的任务以调整范围或提供额外指导。

你探索了以下概念：

- 浏览任务控制中心和智能体页面，监控云智能体任务；
- 重定向正在进行的会话，要求添加其他功能。

Copilot 正在完成无障碍功能，接下来我们将[迭代改进 Copilot 创建的拉取请求][next-lesson]。

## 资源

- [Copilot 智能体页面][agents-page]
- [关于自定义智能体][custom-agents]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代改进 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
