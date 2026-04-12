---
title: Daily Report Master Agent Brainstorm v0
date: 2026-04-12
tags:
  - daily-lane
  - signals-engine
  - feishu
  - obsidian
  - agent
status: draft
source_repo: https://github.com/T0UGH/knowledge-vault
---

# Daily Report Master Agent Brainstorm v0

记录时间：2026-04-12 14:26 CST
状态：**brainstorm 记录，暂不进入实现**

## 这轮讨论要解决什么

目标不是再拼几个松散定时任务，而是定义一个**单一 cron 主 agent**，让它真正对每天日报结果负责。

这个主 agent 需要接管：

1. 当天 signals 采集
2. 当天日报是否成立的判断
3. 日报生成与发布
4. 必要时的运维通知
5. 最终日报归档到 Obsidian（后续实现）

---

## 当前共识

### 1. 一个 cron，一个 owner

- 只保留一个 cron 主入口
- 主 agent 是唯一 owner
- 它不是薄调度器，而是“值班主编 + 调度员”

### 2. 交付哲学：强 delivery

- 这套系统的目标是**稳定、高频、诚实地交付日报**
- 哪怕只有 **1 条有用内容**，也应该发
- 短日报是正常形态，不需要为了“像日报”去硬凑
- blocked 应该是罕见状态

### 3. 内容层优先于系统层异常

- “值不值得看”主要由内容层判断
- `signals-engine` 负责抓取、结构化、准备候选输入
- Editor / 内容层负责判断今天是否至少有 1 条值得进入日报的内容
- 系统层异常不应轻易压过内容层结果

### 4. 主聊天是唯一交付面

- 日报发到主聊天
- 运维通知也发到主聊天
- 但运维通知只在必要时出现，不要每天刷屏

### 5. 运维通知只讲状态，不做内容总结

运维通知只负责：

- 状态
- 异常
- 最终产物链接

明确不做：

- 内容摘要
- 再讲一遍日报重点

### 6. 正常 / 降级 / 阻断

#### 正常
- 有可发内容
- 没有值得额外打扰用户的异常
- 发日报，带一点轻量存在感

#### 降级
- 仍有可发内容
- 但存在值得通知用户的异常或缺口
- 发日报
- 日报顶部放轻量降级标记
- 主聊天额外发运维通知

#### 阻断
只在两种情况下成立：

1. 日报里没有任何有用内容
2. 一个 lane 都没出 signal

对外优先表达为：

- **今天没有日报输入 / 未生成日报**

不在主流程里过早归因为“采集失败”或“今天真没内容”。

### 7. 主 agent 的内部组织

外部只有一个 owner，但内部可以有少量明确子角色：

- Collect runner：跑 signals-engine，返回结构化结果
- Editor：判断内容价值，生成日报草稿
- Humanizer：生成最终人类可读文本
- Publisher / Archiver：负责 Feishu 发布与后续 Obsidian 归档
- Master agent：收集阶段结果并做最终裁决

### 8. Obsidian 的角色

Obsidian 只承担一个角色：

> **最终日报归档库**

当前共识：

- 只归档最终日报
- 不归档运维信息
- 不归档中间草稿
- 归档内容以 final report 正文为真源
- 允许加一层轻量 Obsidian metadata 壳
- 时机是 **Feishu 成功后再归档**
- 归档失败不影响当天日报成功

### 9. 主 agent 只主要处理“今天”

- 默认只对今天负责
- 不把多日趋势、连续失败分析压进主流程
- 跨天健康追踪如果以后要做，应作为附加能力

---

## 目前最关键的设计骨架

可以先把主流程理解成：

1. Collect：运行当天 signals 采集
2. Assess input：判断是否存在日报输入
3. Edit：内容层判断是否至少有 1 条有用内容，并生成草稿
4. Humanize：生成最终日报文本
5. Verdict：主 agent 判定 normal / degraded / blocked
6. Publish：发 Feishu 日报；必要时发运维通知
7. Archive：后续把最终日报落到 Obsidian

---

## 当前仍待继续 brainstorm 的问题

### 1. 正常日的“轻量存在感”具体长什么样
- 是一句“今日日报已生成 + 链接”
- 还是再带一点 lanes 信息

### 2. 哪些异常配得上“降级通知”
- blocked 的标准已经很窄
- 但 degraded 的边界还没彻底量化

### 3. 日报顶部的轻量降级标记长什么样
- 要足够透明
- 但不能把正文搞成运维面板

### 4. Obsidian note 的 metadata 壳长什么样
- 路径
- frontmatter
- tags
- 命名规范

### 5. 主 agent 的结构化 verdict 长什么样
- final status
- report path
- feishu link
- degraded reason
- archive result

---

## 一句话总结

这轮不是在讨论“再加几个定时任务”，而是在定义一个真正对每天结果负责的**daily report master agent**：

- 一个 cron
- 一个 owner
- 强 delivery
- 内容优先
- 必要时才运维冒头
- Feishu 是主交付面
- Obsidian 是最终日报归档层
