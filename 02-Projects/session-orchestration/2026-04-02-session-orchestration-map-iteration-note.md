---
title: session-orchestration-map 迭代记录（2026-04-02）
date: 2026-04-02
project: session-orchestration
status: done
tags:
  - session-orchestration
  - prototype
  - visualization
  - loops
---

# session-orchestration-map 迭代记录（2026-04-02）

## 背景
这次迭代的目标，不是再抽象讨论“多 session 编排”方法论，而是把贵平真实使用中的工作流，画成一张**能看出顺序、并行、回环、汇总、条件回流**的单页展示图。

在线页面：
- https://t0ugh.github.io/session-orchestration-map/

代码仓库：
- https://github.com/T0UGH/session-orchestration-map

## 这轮迭代里确认下来的关键判断

### 1. 卡片节点本身不是主问题
用户后期反馈里比较明确的一点是：
- 卡片/节点样式不是最核心的问题
- 真正的问题是：**小回环没有被可视化出来**
- 之前很多地方只是用文字写“这是个 loop”，再配箭头说明，但用户第一眼并不能“看见回环”

这意味着：
- 不需要全盘推翻第二版的整体布局
- 重点应该放在**关键模块内部的 loop 可视化**

### 2. 第二版是更合适的结构基底
后续方向明确收敛到：
- 回到第二版的整体布局和分区逻辑
- 不再沿“全图硬画复杂拓扑”的方向继续堆复杂度
- 保留第二版的可读性，再做局部增强

### 3. 关键改动应该发生在模块内部
这轮最终被用户认可的方向是：
- 保留第二版总体结构
- 在 Design / Agent Plan / Implementation 这些模块内部，直接画出小回环
- 让 loop 由步骤节点本身组成，而不是由说明文字代替

## 最终收敛下来的页面改法

### A. 保留第二版整体结构
保留了：
- Demand / Intent
- Design Convergence Loop
- Agent / Task Split
- Agent Plan Loops
- Implementation Loops → Global Reviewer
- Verification / Conditional Return

也保留了第二版比较清楚的阶段分层，而没有再改成过密的大拓扑图。

### B. 给关键模块加入内部 micro-loop
最终加入了三类内部小回环：

#### Design 模块内部回环
内部步骤表达为：
- 多路找问题
- 逐条讨论
- 技术评审 / 新问题
- 带问题回查 → 更新 design

#### Plan Loops（并排三组）
统一成：
- 出计划
- 评审
- 讨论
- 放行判断

#### Implementation Loops（并排三组）
统一成：
- 编码
- 评审
- 汇总
- 修正

### C. 小回环步骤节点视觉上收成彩色椭圆
后续又进一步收敛到：
- 每个步骤都做成明显的小椭圆节点
- 步骤文字放大
- 椭圆尺寸放大
- 给不同角色加轻微区分色

当前约定：
- Claude：浅蓝
- Codex：浅绿
- Human：浅金
- Reviewer：浅粉

这一步很关键，因为它把“标签”变成了“步骤节点”，用户对这一版基本认可。

### D. 并排 loop 的步骤文案完全统一
最后一轮又专门修了一次视觉规格不统一的问题。

Plan Loops 三组统一为：
- 出计划
- 评审
- 讨论
- 放行判断

Implementation Loops 三组统一为：
- 编码
- 评审
- 汇总
- 修正

这样 A / B / C 三个并行 loop 在视觉上和语义颗粒度上都统一了。

## 本轮过程中有价值的方法论

### 1. 用户说“太乱、看不懂”时，不要继续加说明
正确动作不是补解释，而是承认：
- 信息密度太高
- 拓扑关系超出第一眼可读范围
- 应该先回到更可读的结构基底，再逐步增强

### 2. “看不出回环”不等于要把整页都画成大环
真正有效的办法是：
- 保持大结构稳定
- 让关键模块内部自带 loop 结构
- 用模块内小环替代“文字说明这是环”

### 3. 视觉收尾阶段要收规格，而不是再碰结构
当结构基本成立后，最后决定观感的往往不是拓扑本身，而是：
- 字号
- 节点形状
- 色彩区分
- 并排模块的文案统一性

这次最后几轮迭代，实际上就是在做规格收束，而不是结构重构。

## 对应代码提交
session-orchestration-map 仓库里，这轮收敛相关的关键提交包括：
- `7903f9e` — `refactor: add visible internal micro-loops to modules`
- `8f81dbf` — `style: enlarge loop step text and emphasize oval step nodes`
- `8dfb4bc` — `style: enlarge colored oval step nodes in micro-loops`
- `4580eb0` — `style: unify parallel loop step labels`

## 当前结论
这版页面可以先收：
- 结构基底回到了可读性更好的第二版
- 小回环被真正画进模块内部
- 步骤节点已经从“标签”升级成“彩色椭圆步骤节点”
- 并行 loop 的文案规格已经统一

后续如果再迭代，优先应该做的是局部精修，而不是再推翻整个页面结构。
