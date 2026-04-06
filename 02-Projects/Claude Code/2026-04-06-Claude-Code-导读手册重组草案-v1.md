# Claude Code 导读手册重组草案 v1

日期：2026-04-06  
目的：先按“读者理解路径”重建全书结构，再反推现有文章应归入哪一卷。  
说明：这是一版**重组草案**，不是最终定稿。重点先解决“卷的主问题”和“现有文章该落哪卷”。

---

## 一、重组原则

不再先按“现在已经有六卷”来整理，而先按**认知台阶**来组织：

1. 先理解 Claude Code 是什么系统
2. 再理解一次 agent turn 怎么跑起来
3. 再理解模型意图怎么落成真实执行
4. 再理解上下文、状态与持续工作
5. 再理解扩展机制与多代理能力
6. 最后理解用户入口与控制层

也就是说：

> 卷不是按源码目录切，而是按读者脑中的理解坡度切。

---

## 二、建议的新六卷主问题

### 卷一：系统全景与核心对象
主问题：**Claude Code 到底是什么系统？它由哪些核心对象组成？**

### 卷二：一次 Agent Turn 怎么跑起来
主问题：**用户输入怎么变成一次完整的 agent turn？**

### 卷三：工具系统与执行能力
主问题：**模型决定调用能力之后，runtime 怎么把它落成真实执行？**

### 卷四：上下文、状态与持续工作
主问题：**Claude Code 怎么维持上下文、状态和长期运行，而不是越跑越乱？**

### 卷五：扩展机制与多代理能力
主问题：**skills / agents / subagents / MCP / plugins 这些能力是怎么装进系统的？**

### 卷六：命令入口与控制层
主问题：**用户到底通过哪些入口驱动这套系统，这些入口分别会走向哪条运行路径？**

---

## 三、现有文章重组归卷草案

下面不是“现在在哪一卷”，而是“按新设计，我建议它应该在哪一卷”。

---

# 卷一：系统全景与核心对象

## 这一卷应该收什么
先给读者一个全景图：Claude Code 是什么、tool/agent/skill/command/prompt 这些对象分别站在哪。

## 建议放入文章

### 1. 总览与系统定位
- `volume-1/01-tool-overview-and-bash-entry.md`
  - 建议用途：保留为全书早期入口，但要弱化“只讲 tool”，强化“Claude Code 如何把模型能力落成执行能力”的总图作用
- `volume-2/02-full-agent-turn.md`
  - 建议用途：放到卷一结尾或卷二开头都可以；如果卷一需要一个全景总结，它非常适合做“全流程鸟瞰”
- `volume-3/01-main-loop-glossary.md`
  - 建议用途：改成全书术语表/系统对象词汇表，放卷一非常合适

### 2. 核心对象边界
- `volume-1/30-skill-vs-agent.md`
- `volume-1/31-prompt-as-instruction-layer.md`

这两篇不该埋在后面。它们本质上回答的是：
- skill 是什么
- agent 是什么
- prompt 在系统里是什么层

这些更像全景卷里的对象边界文章。

---

# 卷二：一次 Agent Turn 怎么跑起来

## 这一卷应该收什么
回答：一条用户输入如何进入系统、被处理、进入 query、再走完一轮。

## 建议放入文章

### 1. 主循环主线
- `volume-2/01-from-query-to-tool-call.md`
- `volume-2/03-queryengine-entry.md`
- `volume-2/04-query-main-loop.md`
- `volume-2/11-queryengine-example-walkthrough.md`
- `volume-2/12-queryengine-example-plain.md`

### 2. 输入进入系统前的准备与分流
- `volume-2/05-process-user-input.md`
- `volume-2/06-get-attachment-messages.md`
- `volume-2/07-messages-normalization.md`
- `volume-2/13-system-user-contexts.md`

### 3. 若需要一个总览式收尾
- `volume-2/02-full-agent-turn.md`（如果不放卷一）

---

# 卷三：工具系统与执行能力

## 这一卷应该收什么
回答：Tool 抽象是什么、tool_use 怎么落成执行、本地执行能力怎样被 runtime 接住。

## 建议放入文章

### 1. Tool 抽象与执行主线
- `volume-1/02-tool-system-overview.md`
- `volume-1/09-how-tools-enter-runtime.md`

### 2. 本地工具能力线
- `volume-1/03-bashtool-is-not-just-shell.md`
- `volume-1/04-filereadtool.md`
- `volume-1/05-fileedittool.md`
- `volume-1/06-filewritetool.md`
- `volume-1/07-greptool.md`
- `volume-1/08-toolsearchtool.md`

### 3. 权限相关但紧贴工具执行的文章
这里有两种放法，我当前倾向：
- **核心执行相关的权限判断留卷三**
- **更制度化的权限系统分析放卷六或单列安全卷（如果后面增卷）**

所以暂时可候选：
- `volume-5/02-tool-execution-and-permission-decision.md`
- `volume-5/03-bashtool-permission-model.md`

它们和 Tool/BashTool 的执行主线粘得很紧。

---

# 卷四：上下文、状态与持续工作

## 这一卷应该收什么
回答：为什么 Claude Code 能长期运行、压缩上下文、恢复会话，而不是每轮都“忘光重来”。

## 建议放入文章

### 1. 上下文构造与 system prompt
- `volume-2/08-system-prompt-and-context.md`
- `volume-2/09-collapse-keeps-working.md`
- `volume-2/10-context-collapse-read-time-projection.md`

### 2. 长期运行与压缩线
- `volume-3/02-main-loop-sequence-diagram.md`
- `volume-3/03-does-long-context-recompute-every-turn.md`
- `volume-3/05-compact.md`
- `volume-3/06-microcompact.md`
- `volume-3/07-compact-vs-microcompact.md`
- `volume-3/08-snip.md`
- `volume-3/09-cache-edits-and-content-replacement.md`
- `volume-3/10-what-snip-removes.md`
- `volume-3/11-four-context-forms-side-by-side.md`

### 3. 会话存储与恢复
- `volume-3/04-session-storage.md`
- `volume-3/13-conversation-recovery.md`
- `volume-3/14-session-restore.md`
- `volume-3/15-session-line-conclusion.md`

### 4. 可能放卷末的高层总结
- `volume-3/12-twenty-agent-design-takes.md`

这篇像整卷高层抽象，适合放卷四末尾作为“为什么这套上下文系统长这样”的设计总结。

---

# 卷五：扩展机制与多代理能力

## 这一卷应该收什么
回答：Claude Code 怎么把 skills、agents、subagents、MCP、plugins 这些外部能力装进同一个系统。

## 建议放入文章

### 1. Agent / subagent / team 能力线
- `volume-1/10-agenttool.md`
- `volume-1/11-runagent-assembly-line.md`
- `volume-1/12-forksubagent.md`
- `volume-1/13-loadagentsdir.md`
- `volume-1/14-builtinagents.md`
- `volume-1/18-forkedagent.md`
- `volume-1/19-runagent-skill-mainline.md`
- `volume-6/01-team-runtime-position.md`
- `volume-6/02-team-lifecycle.md`
- `volume-6/03-inprocess-teammate-task.md`
- `volume-6/04-mailbox-idle-shutdown.md`
- `volume-6/05-local-remote-teammate-boundaries.md`
- `volume-6/06-team-runtime-conclusion.md`

### 2. Skill 能力线
- `volume-1/15-skilltool-bridge.md`
- `volume-1/16-loadskillsdir.md`
- `volume-1/17-skilltool-execution-entry.md`
- `volume-1/21-skill-frontmatter-fields.md`
- `volume-1/22-frontmatter-runtime-interface.md`
- `volume-1/23-good-runtime-skill.md`
- `volume-1/24-skillify.md`
- `volume-1/27-skills-conclusion.md`
- `volume-1/28-skill-name-description.md`
- `volume-1/29-when-to-use-vs-description.md`

### 3. MCP / hooks / plugins
- `volume-4/01-mcp-runtime-entry.md`
- `volume-4/02-mcptool-call-chain.md`
- `volume-4/03-cli-plus-skill-vs-many-mcp.md`
- `volume-4/04-mcp-auth-state-machine.md`
- `volume-4/05-mcp-permission-boundary.md`
- `volume-4/06-hooks-runtime-entry.md`
- `volume-4/07-pretooluse-posttooluse-hooks.md`
- `volume-4/08-sessionstart-stop-hooks.md`
- `volume-4/09-hooks-conclusion.md`
- `volume-4/10-plugin-capability-surface.md`
- `volume-4/11-plugin-loader.md`
- `volume-4/12-plugin-attachment-points.md`
- `volume-4/13-plugin-validate-schema-policy.md`
- `volume-4/14-plugin-cli-install-marketplace.md`
- `volume-4/15-plugin-conclusion.md`

这卷会比较肥，但它的主问题至少是统一的：
> 系统怎样长出更多能力。

---

# 卷六：命令入口与控制层

## 这一卷应该收什么
回答：用户到底是通过哪些入口驱动这套系统，不同入口分别进入哪条运行路径。

## 建议放入文章

### 1. 命令入口
- `volume-1/20-processpromptslashcommand.md`

### 2. 校验 / 调试 / 控制面辅助文章
这部分还不够丰满，当前可暂放：
- `volume-1/25-verify.md`
- `volume-1/26-debug.md`

### 3. 权限系统（另一种放法）
如果你希望卷三尽量只保留“执行能力”本体，而把制度化控制都抽出来，那这一卷还可以收：
- `volume-5/01-permission-system-overview.md`
- `volume-5/02-tool-execution-and-permission-decision.md`
- `volume-5/03-bashtool-permission-model.md`
- `volume-5/04-path-permissions-and-persistence.md`
- `volume-5/05-policy-limits.md`
- `volume-5/06-permission-system-conclusion.md`

也就是说，卷六现在有个分叉：

### 分叉 A：卷六偏“入口控制”
那它会偏轻，需要再补材料。

### 分叉 B：卷六偏“控制与约束系统”
那它就可以把 permissions 收进来，卷会厚实很多，也更完整。

目前我**更倾向分叉 B**，因为这样卷六的主问题会更稳：

> **用户如何触发系统，系统又如何对这些能力施加控制与约束。**

---

## 四、当前最明显的问题

### 1. 现有 volume-1 实际上混了三卷内容
现在的 volume-1 里同时混着：
- 工具系统（应去卷三）
- agent / skill（应去卷五）
- command / verify / debug（应去卷六）

也就是说，现有卷一不是“卷一太大”，而是**它其实是三卷混装**。

### 2. 现有 volume-4、5、6 的边界还不够稳
- volume-4：MCP / hooks / plugins，整体偏“扩展能力”
- volume-5：permissions，既像执行制度，也像控制层
- volume-6：team runtime，明显属于 agent / multi-agent 能力

所以后半部分大概率要重排。

### 3. 真正最该先做的不是改卷名，而是重写每卷 README / index
因为只有 README 先回答清楚：
- 这卷解决什么问题
- 这卷不解决什么问题
- 这些文章为什么在这里

后面的文章迁移才不会乱。

---

## 五、我当前最推荐的下一步

### 第一步
先不动正文，先写一版：
- 六卷新版 README 主问题说明

### 第二步
再决定：
- permissions 放卷三还是卷六

### 第三步
最后再做：
- 文章迁移 / 导航调整 / 卷顺序更新

---

## 六、一句话判断

> 现有六卷里，最混的是卷一；最不稳的是后半三卷。真正的重组工作不是“微调目录”，而是先把每一卷的主问题写死，再把文章重新归位。
