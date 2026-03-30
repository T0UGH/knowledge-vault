# 2026-03-30 为什么 OpenClaw 的 subagent 和 Claude Code 的 subagent 感觉很不一样

## 一句话结论

OpenClaw 的 subagent 更像：

> **平台级、被正式托管的子 session 节点**

而 Claude Code 的 subagent 更像：

> **当前 coding workflow 里的内置协作者 / 工具化分身**

所以两者的“手感”不一样，不是错觉，而是抽象层级就不同。

---

## 一、OpenClaw subagent 的正确心智

在 OpenClaw 里，不要把 subagent 理解成“再叫一个脑子帮我干活”。

更准确的理解是：

> **父 session 创建一个子 session，让它在平台托管的独立会话里完成某个子任务；子任务完成后，结果自动回到父 session，由父 session 继续编排。**

这意味着 OpenClaw 的 subagent 是 session topology 的一部分，而不是单纯工作流内部的一个函数调用。

---

## 二、为什么父 session 必须掌控回环次数

如果一个流程里有：
- Editor
- Reviewer
- 最多一次返工

那么真正能掌握全局状态的只有父 session。

原因：
1. 只有父 session 知道当前是第几轮
2. 只有父 session 知道是否还允许打回一次
3. 只有父 session 知道什么时候必须结束流程
4. 只有父 session 应该拥有最终裁决权

如果让子 session 自己互相拉扯，很容易出现：
- 无限制回环
- 责任不清
- 状态分裂

所以正确模式是：

> **子 session 只做任务，父 session 负责状态机。**

---

## 三、为什么不要轮询 child status

OpenClaw 的 subagent 机制天然更适合 **push-based completion**，而不是父 session 自己轮询子 session 是否完成。

如果轮询，会有几个问题：
1. 复杂度徒增：轮询、sleep、timeout、retry、半成品读取都会冒出来
2. 容易拿到未完成结果
3. 和 OpenClaw 现成的 announce/completion 模型重复

OpenClaw 已经提供的正确路径是：
- 子 session 完成
- 平台自动 announce 给父 session
- 父 session 接着编排下一步

所以：

> **不要把 OpenClaw subagent 写成自己维护状态轮询的 job runner。**

---

## 四、为什么结果必须 push 回父会话

父 session 应该是整个流程的：
- 总账本
- 决策中心
- 最终对用户的交付面

如果子 session 的结果不回父 session，而是只留在子 session 自己的 transcript 里，父 session就会失去：
- 流程连续性
- 结果收口点
- 用户可见的一致主线

这样一来，父 session还得自己去：
- 找 child session key
- 读 child transcript
- 猜哪个输出才是最终结果

这是反模式。

正确做法是：

> **子 session 完成后，把结果 push 回父 session，让父 session 继续成为唯一可信的流程主线。**

---

## 五、为什么 session key / announce delivery 很重要

这是 OpenClaw 与 Claude Code 差异最大的地方之一。

### 1. session key 决定“事件送给谁”

在 OpenClaw 里，很多能力最终都依赖一个真实 session runtime 去消费：
- system event
- subagent 结果
- heartbeat
- transcript 写入

如果 session key 是假的、空的、或没有 active runtime 的 session，就会出现一种非常迷惑的情况：
- 日志看起来成功
- `enqueue` 也成功
- 但最终没人消费
- 用户什么都看不到

所以 session key 不是随便一个字符串，而是：

> **一个真实可消费的会话目标。**

### 2. announce delivery 决定“结果怎么回父会话”

announce delivery 的意义是：
- 子 session 完成后
- 平台自动把完成结果送回父 session
- 父 session 接着往下编排

这使得父 session 不需要自己轮询、抓 transcript、拼接结果。

所以：
- session key 决定送给谁
- announce delivery 决定怎么送回去

这两个共同构成 OpenClaw subagent 的基础设施。

---

## 六、把整个流程串起来看

以“日报草稿 + reviewer 审核 + 最多返工一次”为例：

### Step 1：父 session 启动 Editor 子 session
父 session 决定启动一个子任务，拉起 Editor。

### Step 2：Editor 在自己的子 session 中完成草稿
它有自己的上下文、自己的运行空间。

### Step 3：Editor 完成后，结果自动回到父 session
父 session 收到 Editor 的产出，而不是自己去找 child transcript。

### Step 4：父 session 再启动 Reviewer 子 session
把：
- 原始输入
- Editor 草稿
- reviewer rubric
一起交给 Reviewer。

### Step 5：Reviewer 完成后，结果再回到父 session
父 session 收到 reviewer 的结论：
- approve
- revise
- reject

### Step 6：父 session 决定是否回环
如果：
- reviewer 建议 revise
- 且返工次数还没超限

则父 session 再起一次 Editor。

### Step 7：父 session 最终输出结果
最终用户只看父 session，就能看到：
- 调度过程
- reviewer 结论
- 最终结果

这就是 OpenClaw subagent 最自然的工作流。

---

## 七、为什么它和 Claude Code 很不一样

Claude Code 的 subagent 更像是：
- 当前 coding 任务里的 Planner / Reviewer / Critic
- 一个工作流内部分工者
- 很像同一个任务里的“另一个脑子”

所以它天然更偏：
- task-centric
- coding-centric
- workflow-centric

而 OpenClaw 的 subagent 更偏：
- session-centric
- orchestration-centric
- runtime-centric

所以两者差异可以浓缩成：

### Claude Code subagent
> workflow-native collaborator

### OpenClaw subagent
> runtime-native child session

这就是为什么 OpenClaw 更适合：
- 长期在线 agent
- 多 session 编排
- 插件 / cron / mailbox / heartbeat 等平台级流程

但也因此更重，更需要你先想清楚：
- 父 session
- 子 session
- session key
- completion announce
- transcript / visibility

---

## 八、工程判断

如果问题域是：
- 代码修改
- review
- plan/fix
- 仓库内工作流

Claude Code 的 subagent 心智通常更顺手。

如果问题域是：
- 长期在线 agent
- 多会话协作
- mailbox / heartbeat / plugin / cron
- orchestrator 调度多个子任务

OpenClaw 的 subagent 反而更自然。

所以不要拿 Claude Code 的“副驾驶”心智直接套 OpenClaw。

更准确的理解应该是：

> **OpenClaw 的 subagent 不是附属功能，而是平台正式托管的子会话节点。**

---

## 九、一句话收口

OpenClaw 的 subagent 之所以让人感觉和 Claude Code 很不一样，是因为它不是“临时帮手”，而是：

> **一个由父 session 显式调度、由平台托管、能通过 announce delivery 回到父 session 的正式子会话。**
