# 从零开始两天构建一个 Claude Code：带你拆解 AI CLI 的每一层

> 来源：https://x.com/IceBearMiner/article/2037888800341610684
> 作者：小八 @IceBearMiner
> 发布：Mar 28
> 统计：311次转帖、1411喜欢、3205书签、470007次观看

---

## 前言

前两天突发奇想：一个生产级的 agentic CLI 到底需要哪些组件？每一层的具体怎么实现？SSE 缓冲区怎么管理、system prompt 怎么分段、工具权限怎么拦截、上下文满了怎么压缩。这些问题靠读文档回答不了，靠逆向混淆代码效率极低。

所以选择了另一条路：以 Claude Code 为参照系，从零重建一个功能等价的实现——纯 TypeScript，零框架，唯一的依赖是 fast-glob（因为原生 glob 在跨平台路径处理上有已知缺陷）。

两天之后的结果是 46 个文件，一万行 TypeScript。这篇文章记录的是这个过程中每一层的技术决策和实现细节。

---

## 整体架构

核心流程：
```
用户输入 → 组装请求 → API 调用（SSE）→ 解析响应事件流 → 根据 stop_reason 决定分支：
  - end_turn — 输出文本，结束本轮
  - tool_use — 执行工具调用 → 将 tool_result 追加到 messages → 反复迭代
```

目录结构按层划分（六层依赖单向，core/ 不依赖 ui/，tools/ 不依赖 skills/）：

| 目录 | 职责 |
|------|------|
| `core/` | 引擎层：Agent Loop、SSE 客户端、context 管理、compact 逻辑 |
| `tools/` | 工具系统：所有内置工具实现 + MCP 客户端 |
| `ui/` | 终端渲染层：流式文本输出、进度指示、颜色主题 |
| `plugins/` | 扩展系统：运行时注入工具和 hook |
| `skills/` | 技能库：对应 Claude Code 的 slash command 高级功能 |
| `commands/` | `/` 前缀命令解析和分发 |

**技术选型核心理由**：Node.js 22 的原生能力。fetch 和 ReadableStream 已稳定，不需要 node-fetch。唯一依赖 fast-glob（跨平台路径 + gitignore 性能比原生实现好一个数量级）。

---

## 多平台 LLM 兼容

默认支持御三家和自定义平台，自定义模型（OpenAI 兼容格式）附加支持。部分本地模型（Ollama、LM Studio）暴露 OpenAI 兼容接口，事件格式不同。

处理方式：在 SSE 客户端初始化时传入 `format: 'openai'` 参数，在事件解析层做格式适配，将 OpenAI 的 delta 结构翻译成统一的内部事件类型。**Agent Loop 层完全不感知 API 格式差异**。

---

## System Prompt 分段架构与 Prompt Caching

最直觉的 system prompt 写法是一个大字符串，但在生产环境有两个显著缺陷：
1. 每轮 system prompt 几乎不变，但以单字符串传入，API 缓存无法有效命中
2. 部分内容（当前目录、Git 状态、CLAUDE.md）每轮变化，与静态内容混在一起污染缓存

**解决方案**：将 system 参数设为 block 数组，每个 block 可以独立设置 cache_control。

**静态段**（进程生命周期内不变，打上 cache_control 后首次写入缓存，后续命中）：
- 身份声明（参考龙虾的 User、Soul）
- 工具使用规范（何时用 Bash vs 读文件、何时拒绝执行）
- 编码风格（规范、注释原则）
- 安全执行规则（禁止执行的命令类型）

**动态段**（不带 cache_control，每轮重新计算）：
- 当前工作目录和系统环境
- Git 仓库状态（git status 输出）
- CLAUDE.md 内容
- MCP 服务器的自定义指令

完整请求体的 system 字段最终是有序 block 数组，顺序固定：身份 → 工具指南 → 编码规范 → 安全规则 → 风格指南 → 环境信息 → Git 上下文 → CLAUDE.md → MCP 指令。**排在前面越稳定，缓存命中率越高。**

---

## Agent Loop：while 循环背后的状态机

骨架是一个有上限的 while 循环，**最大迭代次数 25 次**。

每次迭代流程：
1. 检查是否需要 compact
2. 构建完整 prompt
3. 发起流式请求
4. 实时处理事件流
5. 检查 stop_reason — tool_use 则执行工具，end_turn 或 max_tokens 则结束循环
6. 构建 tool_result message，追加到 messages 数组，进入下一轮

**工具执行管线六阶段**：
1. `renderToolCall` — 终端展示将要执行的工具名和参数
2. `permissionCheck` — 根据工具类型和参数决定是否需要用户确认
3. `preHook` — 插件系统的前置拦截点
4. `checkpoint` — 破坏性操作前快照相关文件状态
5. `executeTool` — 调用实际工具函数
6. `postHook` — 插件后置钩子

**自动 compact 机制**：每轮迭代开始时估算 token 数（总字符数除以 4），超过上下文限制 85% 触发压缩——发起独立 API 调用生成摘要，用 `[user: summary, assistant: Understood.]` 替换原 messages。

**Prompt Caching 三个施力点**：
1. system prompt blocks — 静态段走缓存
2. tools 数组 — 最后一个 tool definition 打上 cache_control
3. 最后一条 tool_result message — 作为缓存断点

三层叠加的实际效果：每轮 API 调用只有少量 tokens 是真正的输入计费，大部分走缓存价格（约为正常输入价格的 10%）。

---

## 21 个内置工具

工具入口是 `TOOL_DEFINITIONS`（JSON Schema 数组）+ 单一的 `executeTool` 函数（switch 按工具名分发）。MCP 工具通过 `mcp__` 前缀识别，走独立调用路径。

核心工具实现细节：

| 工具 | 实现要点 |
|------|---------|
| **Read** | fs.readFile + 行号前缀，支持 offset/limit 分页 |
| **Write** | 写入前 `fs.mkdir({ recursive: true })` 确保父目录存在 |
| **Edit** | 精确字符串替换，old_string 必须在文件中唯一出现，多次报错 |
| **Bash** | child_process.exec，120 秒超时，输出超 500 行截断（保留前 200 + 后 100）|
| **Grep** | 自实现 regex 引擎，三种输出模式、上下文行、跨行匹配，不依赖系统 grep |
| **WebFetch/WebSearch** | fetch + HTML 剥离 + 截断，搜索走 DuckDuckGo |

**Deferred Tools**（性能优化）：低频工具（NotebookRead、TodoWrite）不放入每次请求的 tools 数组，标记为 deferred，模型需要时通过 ToolSearch 查询完整 schema，下一轮调用。**将 tools 数组的固定开销降低约 40%**。

---

## 权限系统：三种模式与两阶段分类器

**三种模式**：

| 模式 | 行为 |
|------|------|
| `default` | safe 类工具自动执行，dangerous + write 类需用户确认 |
| `auto` | 绕过所有交互式提示，适合 CI——deny rules 仍生效 |
| `plan` | 只读沙箱，dangerous + write 工具静默拒绝，可先看执行计划 |

**工具分类**（注册时静态声明）：
- `safe` — Read, Glob, Grep, WebFetch, WebSearch 等
- `dangerous` — Bash, Agent（副作用范围不可预测）
- `write` — Write, Edit（文件修改最常见需审计操作）
- `bypass` — PlanMode 切换，始终无需确认

**两阶段分类器增强 auto 模式安全性**：
- **Stage 1** — 纯模式匹配：维护已知安全/危险命令规则表，命中即返回，覆盖 90%+ 情况，零延迟
- **Stage 2** — 处理未覆盖情况：将命令发给 Haiku 模型，返回 allow/deny/ask_user 三种之一。Haiku 延迟约 300-500ms，相比主循环可忽略。ask_user 让自动模式遇到模糊操作时优雅降级

---

## MCP 动态工具与 LSP 集成

**MCP（Model Context Protocol）**：标准化的工具发现协议。工具可独立部署为进程，通过统一接口被任何兼容客户端发现和调用。

协议层：JSON-RPC 2.0 over stdio。启动序列固定：`initialize` 握手 → `tools/list` 获取工具定义数组。McpManager 管理多个 server 生命周期，工具名加 `mcpserver_name` 前缀做命名空间隔离。

**LSP 集成**解决的问题：不是扩展工具，而是给模型提供实时代码诊断信息。LspClient 实现 LSP 客户端侧，Write/Edit 执行后自动通知对应语言服务器，诊断结果作为 `lsp_diagnostics` section 注入下一轮 system prompt。**模型修改代码后立即看到编译器反馈，缩短发现问题到修复问题的路径**。

---

## 插件系统与 Skills

插件回答的问题：如何在不修改 CLI 核心代码的情况下扩展能力？

答案是 **manifest 驱动的目录结构**。每个插件是一个目录，包含 `plugin.json`，声明六类扩展点：skills、agents、hooks、commands、mcpServers、lspServers。

**8 个内置 skill**：

| Skill | 用途 |
|-------|------|
| `commit` | 读取 diff 生成规范化提交消息 |
| `pr` | 生成 PR 描述 |
| `review` | 代码审查 |
| `init` | 扫描项目结构，生成配置文件 |
| `simplify` | 审查变更代码，查找重复/低效逻辑，直接修复 |
| `loop/schedule` | 持续执行或定时触发 |
| `update-config` | 修改 CLI 自身配置 |

Skill 查找优先级：内置 → 插件 → 项目 `.clio/skills/`。项目级可覆盖插件级，但不能覆盖内置（防止安全敏感的内置 skill 被替换）。

---

## Agent Teams：多 Agent 协作

单 Agent 瓶颈在于串行执行。多 Agent 并行能显著缩短完成时间。

**子 Agent** 通过 `executeSubAgent()` 运行（主 agent loop 简化版，排除团队管理工具防止无限嵌套，最多 15 轮）。隔离模式可选：`isolation: "worktree"` 创建 Git worktree，子 Agent 在独立分支操作，完成后主 Agent 决定是否合并。

**后台 Agent**：通过 `run_in_background: true` 标记，Promise 存入 Map，完成时以 tool_result 形式通知主 Agent。

**自定义 Agent**：在 `.clio/agents/*.md` 文件中定义，YAML front-matter 声明工具子集、模型选择、最大迭代次数。

**完整协作流程**：TeamCreate → SendMessage 分发任务 → 等待完成 → 汇总结果 → TeamDelete。**没有消息队列，没有共享状态，只有显式的消息传递**。

---

## Auto Memory：跨会话记忆

LLM 本身无状态，每次对话从空白上下文开始。Auto Memory 用文件系统模拟持久记忆，不引入数据库。

**记忆文件**：带 YAML front-matter 的 Markdown 文件，四种类型：
- `user` — 用户偏好（角色、习惯、专长）
- `feedback` — 行为纠正（用户确认或否定的做法）
- `project` — 项目约定（不可从代码/git 推导的上下文）
- `reference` — 外部资源指针（URL、看板、文档链接）

**MEMORY.md** 是索引文件，每次对话开始时注入 system prompt，模型按需通过 Read 工具读取具体内容。

记忆写入完全复用现有工具链（Write/Edit/Read），零新增代码路径，工具权限模型自动适用。

---

## 结论

> 核心难点在于 Harness Engineering。毕竟调用 API 是十行代码的事，只有把工具调用结果正确地反馈给模型、在流式输出中间插入用户交互、处理长任务里的错误恢复——这些才是真正考验工程能力的地方。

---

## 作者信息

小八 @IceBearMiner
专注模型安全与逆向攻防
分享心得输出干货
交流咨询：t.me/icebear_0828
