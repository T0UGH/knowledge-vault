---
title: OpenClaw 共读计划
date: 2026-04-05
tags:
  - openclaw
  - co-reading
  - architecture
  - agent-harness
status: active
aliases:
  - OpenClaw 源码共读计划
  - OpenClaw 共读索引
---

# OpenClaw 共读计划

## 目标

这轮共读的主目标定为两件事：

1. **吃透 OpenClaw 架构**
2. **顺手提炼 agent harness 方法论**

这不是一轮“按目录旅游式”的源码阅读，而是一轮围绕 **系统主线** 展开的结构化共读。

---

## 为什么先读 OpenClaw

当前 knowledge-vault 里，OpenClaw 相关沉淀已经明显多于 Codex。

这意味着继续读 OpenClaw 的收益更高，因为：

- 它离当前真实协作和真实问题更近
- 已有上下文和判断足够厚，适合继续收束成体系
- 这轮阅读能直接反哺当前 OpenClaw 使用、排障、设计和扩展判断

一句话：

> **OpenClaw 是当前主战场，Codex 是后续外部参照系。**

---

## 本轮共读的总体策略

本轮采用两阶段策略：

## Phase 1：先过主循环

先不急着按问题拆开，而是先回答：

- OpenClaw 一次完整运行是怎么转起来的
- 消息从哪来，进入谁
- 谁持有 session
- 谁驱动 agent
- subagent / memory / gateway 插在哪一层

这一阶段的目的不是穷尽细节，而是：

> **先拿到系统骨架和运行总图。**

### Phase 1 产出

- 一张 OpenClaw 主循环总图
- 一个总览共读 note
- 关键入口文件索引

---

## Phase 2：再问题驱动下钻

在主循环有了之后，再围绕关键问题往下打深。

当前预定的核心问题有四个：

1. **session/runtime 为什么是一等公民**
2. **subagent 为什么是平台托管子会话，而不是普通函数调用**
3. **memory/context 在哪一层注入**
4. **gateway/routing 为什么决定“谁能收到结果”**

这一阶段的原则是：

- 不按目录读
- 不追求文件全覆盖
- 只围绕关键问题追主线

### Phase 2 产出

- 每个核心问题一篇小 note
- 每篇都包含：结论、关键源码位置、机制解释、方法论抽象

---

## Phase 3：方法论提炼

当前这轮共读，不只是为了理解 OpenClaw 本身，也为了沉淀对 agent harness 的长期判断。

这一阶段希望最终收口出类似这样的设计原则：

- 子 agent 应该是 session topology 的节点，不是普通函数调用
- 父 session 应该掌控状态机，而不是让子 session 互相拉扯
- 平台级 runtime 和 workflow 级协作者是两种不同抽象
- 运行时看不见的信息等于不存在
- push-based completion 比轮询更适合托管型多 session 系统

### Phase 3 产出

- 一篇 OpenClaw harness 方法论总结
- 以及后续与 Claude Code / Codex / DeerFlow / Hermes 的对照视角

---

## 为什么采用“先主循环，再问题驱动”

相比一开始就按问题拆，先过主循环有三个明显好处：

### 1. 主循环是“目录的目录”

它能先提供系统地图，避免问题驱动时不断跨文件跳跃、失去全局感。

### 2. OpenClaw 是 runtime 系统，不是单文件工具

如果不先看它是怎么活起来的，后面讨论 session / subagent / memory 时，很容易只记住局部机制，却不明白为什么这些能力要落在这一层。

### 3. 这轮目标是训练 harness 直觉

真正重要的不是“模块清单”，而是：

> **系统是怎么活起来的。**

---

## 推荐阅读节奏

建议每轮共读控制在 **20~40 分钟**，每轮只解决一个明确问题。

每轮固定产出：

- 结论
- 关键源码位置
- 机制解释
- 方法论抽象
- 更新到 Obsidian 共读笔记

这样可以避免阅读失控，也更适合长期积累。

---

## 当前已拍板的方向

### 共读目标

- A：吃透架构
- C：提炼方法论

### 共读形式

采用混合型：

1. 先架构地图
2. 再问题驱动下钻
3. 最后补关键源码导读

### 执行顺序

- **先过主循环**
- **再按问题深挖**

---

## 下一步（待执行）

下一次正式开始共读时，优先做：

1. 先画出一版 **OpenClaw 主循环草图**
2. 再去源码中验证和修正草图
3. 形成第一篇主循环总览笔记

---

## 相关笔记

- [[02-Projects/OpenClaw/2026-03-30-why-openclaw-subagent-feels-different-from-claude-code]]
- [[03-Research/Agent-Systems/2026-04-05-DeerFlow-vs-Hermes-as-Third-Agent]]
- [[02-Projects/OpenClaw/README]]
