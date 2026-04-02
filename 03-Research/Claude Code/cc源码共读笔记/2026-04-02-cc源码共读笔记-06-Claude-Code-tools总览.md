---
title: Claude Code tools 总览
date: 2026-04-02
tags:
  - Claude Code
  - 源码共读
---

# Claude Code tools 总览（临时）

这份先解决一个很实际的问题：Claude Code 里到底有哪些 tools，它们大概是干嘛的。

这一版先给你一个可用地图，不追求把每个工具都讲到源码细节。

---

## 一、文件与代码操作类

### 1. BashTool
运行 shell 命令。支持前台/后台执行、进度回传、大输出持久化、图片输出识别，是 Claude Code 里最重的基础设施型 tool 之一。

### 2. PowerShellTool
Windows 环境下的 PowerShell 执行工具。定位上基本相当于 BashTool 的 PowerShell 版本。

### 3. FileReadTool
结构化读文件。比直接 `cat` 更稳定，适合模型精确读取文件内容。

### 4. FileEditTool
结构化编辑文件。更偏局部修改，不是整文件覆盖。

### 5. FileWriteTool
整文件写入。适合新建文件或全量重写文件内容。

### 6. NotebookEditTool
编辑 notebook / ipynb 一类文档，处理单元格层面的改动。

### 7. GlobTool
按 glob 规则找文件，比如 `**/*.ts`。

### 8. GrepTool
按内容搜索文本，适合在代码库里找关键字、函数名、配置项。

### 9. LSPTool
走语言服务器能力做代码理解，通常比纯文本 grep 更接近语义级分析。

---

## 二、网页与外部信息获取类

### 10. WebFetchTool
抓取网页正文，适合读文章、文档、页面内容。

### 11. WebSearchTool
发起网页搜索，适合查资料、找来源、做外部调研入口。

---

## 三、MCP 生态与外部工具桥接类

### 12. MCPTool
调用 MCP server 暴露出来的具体工具。它是 Claude Code 连接 MCP 工具生态的主桥。

### 13. ListMcpResourcesTool
列出某个 MCP server 上可读资源。

### 14. ReadMcpResourceTool
读取 MCP resource 内容。

### 15. McpAuthTool
处理 MCP 相关认证或授权流程。

---

## 四、skill / agent / runtime 调度类

### 16. SkillTool
把 skill 体系接进 tool 体系的桥。模型可通过它访问 skill 内容或触发 skill 工作流。

### 17. AgentTool
拉起或调用子 agent / worker。多 agent 协作能力的关键入口之一。

### 18. ToolSearchTool
当 tool 很多时，先用它找“应该用哪个 tool”。它是工具发现层，不是执行层。

### 19. REPLTool
与 REPL / 交互执行环境相关的 tool，更偏内部运行机制。

### 20. SyntheticOutputTool
生成合成输出，通常用于 runtime 内部桥接、包装结果或特殊流程。

### 21. BriefTool
生成简短摘要或简报类输出，更像一种面向交互体验的辅助 tool。

---

## 五、任务与后台运行类

### 22. TaskCreateTool
创建任务。

### 23. TaskGetTool
读取单个任务状态。

### 24. TaskListTool
列出任务。

### 25. TaskUpdateTool
更新任务状态或内容。

### 26. TaskStopTool
停止任务。

### 27. TaskOutputTool
读取任务输出，尤其适合后台任务或长任务。

### 28. ScheduleCronTool
创建或管理 cron / 定时任务。

### 29. SleepTool
等待一段时间。通常是流程控制用，不是主要业务能力。

### 30. RemoteTriggerTool
远程触发某类任务或流程，更偏 runtime / bridge 场景。

---

## 六、协作与团队类

### 31. SendMessageTool
发送消息给其他 session / agent / 线程，支撑多 agent 协作。

### 32. TeamCreateTool
创建 team / 多 agent 协作单元。

### 33. TeamDeleteTool
删除 team / 协作单元。

### 34. AskUserQuestionTool
向用户发起澄清问题或交互式提问。

---

## 七、工作模式与工作区管理类

### 35. ConfigTool
读取或修改配置。

### 36. EnterPlanModeTool
进入 plan mode，更偏规划态，不是直接执行态。

### 37. ExitPlanModeTool
退出 plan mode。

### 38. EnterWorktreeTool
进入某个 worktree 或切到隔离工作区。

### 39. ExitWorktreeTool
退出 worktree 场景。

---

## 八、个人组织与辅助类

### 40. TodoWriteTool
写 todo / 更新待办，偏过程管理。

---

## 九、如果按“最值得先理解”来排

### 第一梯队：最核心
- BashTool
- FileReadTool
- FileEditTool
- FileWriteTool
- GlobTool
- GrepTool

### 第二梯队：Claude Code 自己的 runtime 灵魂
- SkillTool
- AgentTool
- ToolSearchTool
- MCPTool

### 第三梯队：任务与协作
- TaskCreateTool / TaskGetTool / TaskListTool / TaskUpdateTool / TaskStopTool / TaskOutputTool
- SendMessageTool
- TeamCreateTool / TeamDeleteTool

### 第四梯队：工作模式与外围能力
- WebFetchTool
- WebSearchTool
- LSPTool
- ConfigTool
- EnterPlanModeTool / ExitPlanModeTool
- EnterWorktreeTool / ExitWorktreeTool

---

## 十、一个最实用的记法

如果把这些 tools 粗暴但实用地压成 5 类，可以这样记：

### 1. 本地执行类
- Bash
- PowerShell
- FileRead
- FileEdit
- FileWrite
- NotebookEdit

### 2. 搜索读取类
- Glob
- Grep
- WebFetch
- WebSearch
- LSP

### 3. 外部系统桥接类
- MCP
- McpAuth
- ListMcpResources
- ReadMcpResource

### 4. agent runtime 类
- SkillTool
- AgentTool
- ToolSearchTool
- REPLTool
- SyntheticOutputTool

### 5. 任务协作类
- Task*
- SendMessage
- Team*
- ScheduleCron
- AskUserQuestion
- TodoWrite

---

## 十一、下一步建议

如果你要继续共读，我建议顺序是：

1. FileReadTool
2. FileEditTool
3. SkillTool
4. AgentTool
5. MCPTool

这样可以把 Claude Code 的本地能力、runtime 能力、外部能力三条线补齐。