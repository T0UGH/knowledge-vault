---
title: Claude Code 共学记录：Skill / Fork / Agent / Review
date: 2026-04-01
tags:
  - claude-code
  - skill
  - fork
  - agent
  - review
  - source-reading
status: in-progress
source_repo: https://github.com/T0UGH/cc
---

# Claude Code 共学记录：Skill / Fork / Agent / Review

记录时间：2026-04-01
对应源码仓：`~/workspace/cc`

这份笔记是把今晚前面几轮讨论收成一个连续记录，避免知识点散落在聊天里。

---

## 1. 这轮共学的主线

今晚主要围绕 Claude Code 的几条线在读和聊：

1. skill 到底是什么
2. skill 的 frontmatter 实际支持哪些字段
3. `agent` 字段到底干什么
4. `context: fork` 到底是什么
5. skill、agent、subagent 三者的边界怎么分
6. 如果已经有 `review-plan` / `review-code` 两个 skill，是否还需要单独抽一个 `reviewer` subagent

整体目标不是把所有源码都啃完，而是先把 Claude Code 里这套 **skill -> fork -> agent -> subagent runtime** 的心智模型搭起来。

---

## 2. 第一层理解：skill 到底是什么

当前比较准确的说法是：

> **skill = 可被系统加载和调用的高阶提示词 / 工作流模块**

不是 plugin，也不是“模型自己会的能力”。

更像：

- 一套方法
- 一份 SOP
- 一个工作流模板
- 一个可被 runtime 发现、加载、筛选、注入的 prompt 资产

相对地：

- **tool** 负责“做”
- **skill** 负责“指导怎么做”
- **SkillTool** 是模型访问 skill 的桥，而不是 skill 的执行器

一句最好记的话：

> **tool 是手，skill 是套路。**

---

## 3. `loadSkillsDir.ts` 带来的关键认识

读 `src/skills/loadSkillsDir.ts` 后，最重要的结论有几条：

### 3.1 skill 最终会变成 `type: 'prompt'` 的 command

这说明 skill 在 runtime 里并不是一套完全独立的对象系统，而是被编译进 Claude Code 已有的 command 抽象里。

### 3.2 skill 不是纯 markdown，而是结构化 prompt command

它不仅有正文，还会带各种 frontmatter 字段，影响：

- 何时使用
- 是否可直接调用
- 允许哪些工具
- 是否条件激活
- 是否 fork 执行
- 是否绑定 agent
- 等等

### 3.3 skill 来源是多层叠加的

包括：

- bundled skills
- user skills
- project skills
- managed skills
- additional dirs
- legacy commands-as-skills

### 3.4 skill 支持 conditional activation 和 dynamic discovery

也就是说，不是所有 skill 一启动就静态加载完。

有些 skill 会根据：

- 当前操作的文件路径
- 命中的 `paths`
- 当前工作目录层级

被动态发现、动态激活。

这一点说明 skill 系统比“普通 prompt 文件”强很多。

---

## 4. 关于 skill format：为什么平时记得只有 `name + description`

这个问题后来单独聊清楚了。

### 4.1 用户记忆没有错

最常见、最对外讲的 skill 最小 format，确实通常只有：

- `name`
- `description`

### 4.2 但 runtime 实际支持更完整的 frontmatter

所以更准确的说法是：

> **最小 format 可以只有 `name + description`，但 loader 实际支持一整套可选字段。**

也就是：

- 文档层常讲 minimal format
- 源码层支持 extended format

这两者不矛盾，只是不同层级。

---

## 5. 目前已确认能被 Claude Code skill frontmatter 解析的字段

根据 `loadSkillsDir.ts` 这轮阅读，当前能较明确确认的包括：

### 基础字段
- `name`
- `description`

### 推荐常用字段
- `when_to_use`
- `arguments`
- `argument-hint`
- `allowed-tools`
- `user-invocable`

### 高级运行时字段
- `model`
- `effort`
- `disable-model-invocation`
- `context`
- `agent`
- `paths`
- `hooks`
- `shell`
- `version`

后面还专门整理过两份笔记：

- 一份结构化速查
- 一份 humanized 版本

核心判断是：

> **常用 skill 通常先用 `name + description + when_to_use + arguments + allowed-tools` 就够了。**

---

## 6. `agent` 字段到底干什么

这轮深追之后，结论很清楚：

> **`agent` 字段不是给普通 inline skill 用的。**
> **它主要是给 `context: fork` skill 用的，用来指定 fork 出来的子 agent 类型。**

关键点：

### 6.1 只有在 fork 场景里才真正有意义

源码定义已经写得很直：

- `context: 'fork'` = run as sub-agent
- `agent` = Agent type to use when forked

### 6.2 loader 只是把它读进 command

`loadSkillsDir.ts` 不在这一层解释 `agent`，只是把它挂到 command 上。

### 6.3 真正消费它的是 forked agent 准备逻辑

在 `src/utils/forkedAgent.ts` 中有关键代码：

```ts
const agentTypeName = command.agent ?? 'general-purpose'
```

说明：

- 如果 skill frontmatter 写了 `agent`
- fork 执行时就优先使用这个 agent type
- 否则默认回退到 `general-purpose`

所以一句话就是：

> **`agent` 是 fork skill 的“执行器选择器”。**

---

## 7. `context: fork` 到底是什么

这轮看完 `command.ts`、`processSlashCommand.tsx`、`SkillTool.ts`、`forkedAgent.ts`、`runAgent.ts` 后，已经可以比较明确地说：

> **`context: fork` 的意思不是“稍微分叉一下”，而是：这个 skill 不在当前主对话里 inline 展开，而是拉起一个独立的 subagent 去执行。**

### 7.1 官方定义已经写明

在 `command.ts` 里：

- `inline` = skill 内容展开进当前 conversation
- `fork` = skill runs in a sub-agent with separate context and token budget

### 7.2 两条入口都会走 fork

- 用户手动 `/skill-name`
- 模型通过 `SkillTool`

只要 `command.context === 'fork'`，都会进入 fork 分支。

### 7.3 fork skill 真正隔离的是这些东西

#### 独立消息上下文
不再把 skill prompt 直接塞给当前主线程继续推，而是生成新的 promptMessages / agentMessages。

#### 独立 token 预算
通过单独 query loop 实现独立预算，不污染主线程上下文窗口。

#### 独立 agentId
真正起了一个新的 agent 实例。

#### 独立可变状态
例如：

- 文件状态缓存
- discoveredSkillNames
- abortController
- toolDecisions
- 一部分 AppState 写入

默认都是隔离的，按需共享。

### 7.4 但 fork 也不是完全断开

它仍然可能：

- 继承一部分 cache-safe params
- 继承 allowed tools
- 共享部分必要回调
- 最终把结果回传给主线程

### 7.5 一句最自然的话

> **`context: fork` 就是“这个 skill 不在主脑里想，另起一个专门的小脑袋去做，做完把结果带回来”。**

---

## 8. 那 fork skill 和 subagent 是不是一回事

这个问题后面专门讨论过。

### 8.1 结论

> **运行时本质非常接近，但抽象层不完全一样。**

### 8.2 更准确的分层

- **subagent** = 执行机制 / 运行时形态
- **fork skill** = 在 skill frontmatter 里声明“这个 skill 应该以 subagent 方式执行”

所以：

> **fork 是 skill 选择 subagent 作为执行方式**
> 而不是一个完全独立的新概念。

### 8.3 最自然的说法

- subagent：系统真的起了一个子 agent 去干活
- `context: fork`：skill 选择用这种方式干活

---

## 9. skill 和 agent 的边界

这是今晚最关键的一个抽象问题。

当前比较清楚的总结是：

> **skill 是任务方法 / 工作流模板，agent 是稳定角色 / 长期人格配置。**

也可以压成一句：

- **skill**：这次怎么做
- **agent**：由谁来做

### 9.1 agent 更像“岗位 / 工种”

例如：

- reviewer
- researcher
- planner
- debugger

它定义的是：

- 长期角色
- system prompt 风格
- 默认能力边界
- 权限/工具/MCP/模型等长期配置

### 9.2 skill 更像“这类任务的 SOP”

例如：

- 怎么 review 方案
- 怎么 review 代码
- 怎么写日报
- 怎么做验证
- 怎么做 bug 排查

它定义的是：

- 任务步骤
- 关注点
- 输出格式
- 什么时候应该用

### 9.3 二者的关系

- agent 可以 preload skills
- skill 可以指定 `context: fork`
- fork skill 可以指定 `agent`

所以它们不是互相替代，而是：

> **agent 是角色容器，skill 是方法模块，subagent 是具体执行实例。**

---

## 10. 关于 review 设计：是否需要单独抽一个 `reviewer` subagent

这个问题最后聊到了比较实战的一层。

前提是：

- 已经有两个 skill
  - `review-plan`
  - `review-code`
- 而且主要是手动调用

### 10.1 当前判断

> **现在不一定需要单独再定义一个 `reviewer` subagent。**

原因：

- 当前真正的差异主要在两个 review skill 的 SOP 不同
- 还没有强到“必须抽出一个稳定 reviewer 角色”的程度

也就是说，目前更像：

- 两套不同的 review 方法

还不像：

- 一个长期稳定、独立存在的 reviewer agent

### 10.2 什么时候值得再抽 reviewer agent

如果以后出现这些情况，就更值得抽：

1. 所有 review 都想共享同一套稳定角色人格
2. review 类 skill 越来越多（不止 plan / code）
3. 想让所有 review 默认都在 fork 子线程执行
4. 想让 reviewer 统一承接 spec / code / postmortem / release 等多种 review

### 10.3 当前最稳的建议

先走轻一点的设计：

#### 方案 1：只有两个 skill
- `review-plan`
- `review-code`

#### 方案 2：两个 skill，但都 `context: fork`
适合想隔离执行、不污染主线程的情况。

#### 方案 3：再定义 reviewer agent，然后 skill 指向它
这是更重、也更完整的抽象，适合 review 体系真的做大后再上。

### 10.4 当前推荐

> **先用方案 1，最多到方案 2。**
> **先别急着上 reviewer agent。**

---

## 11. 当前沉淀出的几个最好记的判断句

### 关于 skill

> skill 是方法，不是能力。

### 关于 tool

> tool 负责“做”，skill 负责“指导怎么做”。

### 关于 fork

> `context: fork` 是让 skill 作为一个独立 subagent 去执行。

### 关于 agent

> agent 是长期角色，skill 是任务方法。

### 关于 `agent` 字段

> `agent` 不是 inline skill 的标签，而是 fork skill 用来选子 agent 类型的字段。

### 关于 review 体系

> 先把 review 方法做成 skill，等 review 角色真的稳定成型后，再考虑抽 reviewer agent。

---

## 12. 当前已写入知识库的相关笔记

今晚围绕这条学习线，已经另外写了几份更细的笔记：

- `2026-04-01-Claude-Code-系统学习路线.md`
- `2026-04-01-Claude-Code-Skill-第一讲-总图.md`
- `2026-04-01-Claude-Code-Skill-frontmatter-字段速查.md`
- `2026-04-01-Claude-Code-Skill-frontmatter-字段速查-humanized.md`
- `2026-04-01-Claude-Code-环境变量有效性校验.md`

这份“共学记录”更像总索引，把前面几轮聊天里的主线判断收在一起。

---

## 13. 下一步可以继续怎么学

如果后面继续沿这条线往下读，最值得的几个方向是：

1. 继续看 `SkillTool.ts`，把 skill 调用侧补全
2. 看 agent frontmatter / preload skills 的完整链路
3. 做一组真实例子对比：
   - 纯 skill
   - 纯 agent
   - `fork + agent` 的 skill
4. 把 review 体系做成一组真正可用的 Claude Code 模板

当前最推荐的实战方向其实是第 3 和第 4 项，因为这样最容易把抽象概念真正变成手感。
