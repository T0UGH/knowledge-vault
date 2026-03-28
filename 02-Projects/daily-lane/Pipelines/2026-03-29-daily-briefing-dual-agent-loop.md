---
title: Daily Briefing Dual-Agent Loop
date: 2026-03-29
tags:
  - daily-lane
  - daily-briefing
  - pipeline
  - multi-agent
  - openclaw
status: draft
source_repo: https://github.com/T0UGH/daily-lane
---

# Daily Briefing Dual-Agent Loop

这份笔记用于记录 2026-03-29 关于日报 pipeline 的一个重要收敛：

> **第一版不要先做显式规则系统，而是先把筛选与归纳权交给一个子 agent，再由另一个子 agent 做审核、打分与最多一次打回。**

这意味着，日报 pipeline 的第一版核心，不是“规则预筛器”，而是一个有止损的双 agent 编辑回路。

---

## 一、这次收敛掉了什么误区

当前最重要的收敛，不是去设计一套 YAML / JSON 规则 DSL，也不是先把 lane 预筛逻辑工程化成一个复杂规则系统。

相反，当前明确选择是：

- **先不做规则系统**
- **先不把筛选权锁死在显式规则里**
- **先让 agent 真正承担编辑判断**
- **再用第二个 agent 去做 reviewer / gatekeeper**

也就是说，第一版的目标不是“把编辑判断尽量脚本化”，而是：

> **先用双 agent 机制把日报的筛选、归纳、审核闭环跑顺。**

---

## 二、当前拍定的日报 pipeline 机制

### Step 1：输入

输入不是“规则预筛后的 candidate”，而是：

- 来自各 lane 的原始或近原始候选池
- 可以按 lane 提供结构化上下文
- 但不先额外造一层复杂规则系统去替 agent 做编辑判断

换句话说，lane 继续负责提供 signal / candidate pool，
但**日报入选与归纳**先交给 Agent 1。

### Step 2：Agent 1 编排

Agent 1 负责：

- 看各 lane 候选池
- 做筛选
- 做归纳
- 排序 / 组织阅读顺序
- 形成顶部重点
- 形成各 lane 展开
- 输出日报草稿

它本质上扮演的是：

> **editor / briefing composer**

### Step 3：Agent 2 审核

Agent 2 负责：

- 检查是否漏掉 lane 必看内容
- 检查是否重复
- 检查推荐理由是否站得住
- 检查是否证据不足
- 检查是否违反 lane 规则或 briefing 约束
- 给出质量评分
- 决定是否打回

它本质上扮演的是：

> **reviewer / quality gate**

### Step 4：最多一次打回

回环约束已经明确：

- 如果 Agent 2 认为不合格，可以打回 Agent 1
- **但最多只允许打回 1 次**
- 第二版如果仍不过关，**不再无限循环**
- 直接把问题暴露出来，交给上层决定

这个约束很关键，因为它把“AI 弹性”和“系统止损”同时保住了。

---

## 三、为什么当前不先做规则系统

这次讨论里最关键的一点，是把“lane 层稳定脚本”与“日报编辑机制”区分开。

### lane 层仍然应该稳定

底层 signal system 的原则没有变：

- lane 负责收集
- lane 尽量是稳定脚本
- lane 不应在召回层引入过多 AI 决策

### 但日报层不必第一版就规则化

日报层面对的是：

- 跨 lane 取舍
- 顶部重点组织
- 重复信息融合
- 推荐理由是否成立
- 结构怎么让人最快抓住重点

这些判断目前还明显处在“编辑经验正在长出来”的阶段。

如果现在就强行把它们提前固化成显式规则系统，风险很高：

- 容易过早 DSL 化
- 容易把还没成熟的编辑判断写死
- 容易为了“工程化”牺牲真实编辑弹性

所以当前更合理的阶段判断是：

> **lane 继续稳定脚本化；日报编排层先 agent 化。**

---

## 四、这套机制的产品本质

当前这套日报 pipeline，不应再理解成：

- 一个统一排序器
- 一个规则预筛器
- 一个“算出 top N” 的简单程序

而应理解成：

> **一个 lane-aware briefing system**

其核心结构已经明确：

- **日报 = 顶部重点 + 按 lane 展开**
- 不是所有来源强行混成一个总榜
- 每个 lane 的语义边界要被保留
- 顶部重点只是跨 lane 的高价值压缩层

这也意味着：

- Agent 1 不是在做“全局统一排序”
- Agent 1 是在做“带 lane 语义的 briefing 编排”
- Agent 2 也不是只看语言质量
- Agent 2 是在看这份 briefing 是否对 lane 结构、证据、覆盖度负责

---

## 五、第一版最小机制定义

如果把这套机制收成最短的一版定义，可以写成：

> 第一版日报 pipeline 不先建设显式规则预筛系统，而是采用双 agent 编辑机制：Agent 1 负责跨 lane 候选池的筛选、归纳与成稿，Agent 2 负责审核、评分与最多一次打回；若第二版仍不合格，则直接暴露问题而不再继续回环。

---

## 六、OpenClaw 里最自然的实现形态

基于当前 OpenClaw 现成能力，这套机制其实和它的 session / subagent 模型是匹配的。

### 1. 现成匹配点：可直接拉起子 agent

OpenClaw 已经有：

- `sessions_spawn`
- `subagents`
- 子 session 自动回传完成结果
- 单 session 串行 agent loop

从本地 OpenClaw 分发代码可以直接看到，系统 prompt 已经明确写着：

- 可以用 `sessions_spawn` 启动 sub-agent
- 子 agent 完成后会**自动 announce** 回父 session
- 默认是 **push-based**，而不是靠轮询追状态
- 不建议用 `sessions_list` / `sessions_history` / sleep 去硬轮询

这说明，如果日报 orchestrator 想同时调 Agent 1 和 Agent 2，OpenClaw 的底层通信与回传机制本身就支持这种工作流。

### 2. 最适合的第一版实现：主会话 + 两个子 session

最自然的第一版形态应该是：

- **主 orchestrator session**
  - 负责收集 lane 输入
  - 调度 Agent 1 / Agent 2
  - 决定是否结束或是否进入唯一一次打回

- **子 session A（Agent 1）**
  - 输入：lane 候选池 + briefing 目标 + 输出格式要求
  - 输出：日报草稿 + 重点 + 各 lane 内容 + 入选说明

- **子 session B（Agent 2）**
  - 输入：同一批 lane 候选池 + Agent 1 草稿 + reviewer rubric
  - 输出：审核结论、问题列表、质量分、是否建议打回

如果 Agent 2 建议打回：

- 主 orchestrator 再把 reviewer feedback 连同原始输入发回 Agent 1
- Agent 1 重做一次
- Agent 2 再审一次
- 无论是否通过，这一轮结束，不再继续回环

### 3. 为什么不要把“打回逻辑”埋进子 agent 自己内部

从 OpenClaw 的 loop 模型看，每个 session 内部本来就是串行 loop。

因此更合理的做法不是：

- 让 Agent 1 自己内部再调 Agent 2
- 或让两个子 agent 自己相互拉扯

而是：

> **让主 orchestrator 显式掌控回环次数。**

这样有几个好处：

- 回环次数容易硬限制为 1
- 父 session 拥有最终裁决权
- 不容易出现两个子 agent 自发无限来回
- 更符合 OpenClaw 当前“父调子、子完成后 push 回父”的结构

---

## 七、OpenClaw 里不建议第一版做成什么

### 1. 不建议先做 lane 规则 DSL

虽然未来可能会有：

- must-see
- top N
- dedupe policy
- source weights
- lane-specific constraints

但第一版不适合先把这些抽成一整套 DSL。

因为当前最需要验证的不是“规则可配性”，而是：

- 双 agent 编辑闭环是否真的提升质量
- reviewer rubric 是否足够稳
- 哪些判断值得未来固化成规则

### 2. 不建议让两个子 agent 自己互相会话

OpenClaw 的能力允许多 session，但从机制稳定性看，日报这个问题更适合：

- orchestrator 掌控流程
- 子 agent 只做明确分工任务
- reviewer 结果回到 orchestrator
- orchestrator 决定是否触发一次返工

而不是做成一个去中心化 agent debate。

### 3. 不建议依赖轮询式状态机

既然 OpenClaw 现成就是 push-based completion announce，第一版就没必要额外造一套：

- 轮询 child status
- 轮询 logs
- 轮询历史

这会让编排变复杂，也违背了 OpenClaw 现有模型。

---

## 八、如果后面要继续工程化，最值得固化的不是筛选规则，而是 reviewer rubric

这次讨论还有一个很重要的含义：

如果后面真的要把这套机制继续工程化，第一优先不一定是“把 Agent 1 的筛选权配置化”，而更可能是：

> **先把 Agent 2 的审核 rubric 稳定下来。**

因为它更像系统边界，而不是编辑创造力。

更值得逐步显式化的，可能是：

- 哪些 lane 内容属于必查项
- 什么叫“证据不足”
- 什么叫“重复”
- 什么叫“推荐理由站不住”
- 什么情况下必须打回
- 质量分的各维度怎么定义

也就是说：

- Agent 1 更像 editor
- Agent 2 更像 rule-backed reviewer

未来真正值得逐步稳定下来的，往往会先是 Agent 2 的审核框架。

---

## 九、一句话版结论

截至 2026-03-29，日报 pipeline 的第一版最合理机制是：

> 不先建设显式规则预筛系统，而是在 lane-aware briefing 的框架下，采用 Agent 1 负责筛选归纳成稿、Agent 2 负责审核评分与最多一次打回的双 agent 编辑闭环；在 OpenClaw 中，这最自然的落点是由父 orchestrator session 通过 `sessions_spawn` 调度两个子 session，并由父层显式控制唯一一次回环。
