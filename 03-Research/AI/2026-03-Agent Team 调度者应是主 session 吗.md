---
title: Agent Team 调度者应是主 session 吗

 date: 2026-03-28
tags:
  - ai
  - agent-teams
  - orchestration
  - multi-agent
  - workflow
---

# Agent Team 调度者应是主 session 吗

## 结论

默认情况下，**Agent Team 的调度者应该是主 session**。

不是因为技术上只能这样，而是因为这是最稳的权力边界：

- 主 session 持有最高上下文
- 主 session 理解用户真实目标
- 主 session 决定何时拆任务、派谁做、何时中断、谁的结果可信、何时算完成

一句话：

> **主 session = orchestrator / owner；其他 agent = 被按角色调用的能力单元。**

---

## 为什么调度者应该是主 session

### 1. 主 session 才有用户真实意图

worker 往往只拿到子任务切片。只有主 session 知道：

- 用户真正想要什么
- 哪些是硬约束
- 哪些只是表面要求
- 当前最该优化什么

### 2. 主 session 才有全局优先级

多 agent 很容易局部最优。只有主 session 能看见：

- 现在真正最重要的是设计、实现、还是验收
- 什么时候该打断当前任务处理别的事
- 哪个结果值得采纳，哪个先放一放

### 3. 主 session 才应承担最终责任

复杂任务里不能让 worker 自己宣布：

- 我做完了
- 我这个最好
- 我说了算

最终拍板应回到主 session。

---

## Agent Team 的最小落地模型

### 角色 1：主 session（Orchestrator / Owner）

职责：

- 接收用户目标
- 维护全局状态
- 决定是否拆任务
- 决定叫哪些 agent
- 汇总结果
- 最终验收 / 请求用户拍板

它不是干所有活，而是负责：

> **团队运行正确。**

### 角色 2：Planner

职责：

- 把模糊目标拆成可执行包
- 定义阶段目标
- 写 contract / checklist / spec draft

适合：

- 任务刚开始
- 方向还没收口
- 需要拆 sprint / phase

### 角色 3：Builder / Executor

职责：

- 真正推进实现
- 产出改动 / 文档 /分析结果

适合：

- 明确子任务
- 已经知道目标和边界
- 需要深入执行

### 角色 4：Reviewer / Evaluator

职责：

- 挑错
- 找缺口
- 核验与 spec/contract 是否一致
- 防止假完成

适合：

- builder 提交结果后
- 准备对外宣称完成前
- 复杂结果需要独立视角时

### 角色 5：Researcher / Context agent

职责：

- 查资料
- 读文档
- 找现有方案
- 汇总背景输入

适合：

- 需求不清
- 技术栈陌生
- 外部资料多

---

## 在当前环境中的现实映射

### 主 session = MT

职责：

- 跟贵平对齐目标
- 维持全局上下文
- 决定何时找 Hex / Claude Code / subagent
- 决定是否继续、暂停、验收、沉淀

### Hex = Builder / Executor

职责：

- 实现
- 排查
- 工程推进
- 具体动手

### 额外临时 agent = Planner / Researcher / Reviewer

按任务临时派：

- 需要方案拆解 → planner
- 需要外部资料 → researcher
- 需要独立审查 → reviewer

---

## 主 session 应如何调度 Agent Team

### Step 1：判定任务类型

先判断这是：

- 简单任务 → 不拆 team
- 中等复杂 → 1 个 worker 足够
- 复杂任务 → 需要 team 分工

### Step 2：识别当前瓶颈

问：

- 现在缺的是理解？
- 还是实现？
- 还是验收？
- 还是上下文补全？

### Step 3：只派当前最需要的角色

不要一上来就 planner + builder + reviewer 全开。

而是按瓶颈派最少角色。

### Step 4：worker 只拿切片，不拿全部世界

每个 worker 的输入应是：

- 明确目标
- 明确边界
- 明确输出格式
- 明确何时算完成

而不是把整段聊天历史全部砸过去。

### Step 5：结果统一回到主 session 汇总

主 session 再决定：

- 采纳
- 打回
- 再派一轮
- 请求用户拍板
- 沉淀文档

---

## 为什么不建议一开始就做去中心化 team

### 不建议 1：worker 再派 worker

例如：

- agent A 再派 agent B
- agent B 再去叫 agent C
- 大家自己互相协调

这很快会失控。

### 不建议 2：一开始就追求 planner/generator/evaluator 全自动流水线

理论上很帅，现实里很容易：

- 成本高
- 调不稳
- 边界模糊
- 最后没人信结果

当前阶段更适合：

> **主 session orchestrated team**

而不是 autonomous team swarm。

---

## 一句话设计原则

> **Agent Team 的调度者默认应该是主 session；其他 agent 不是平权自治成员，而是被主 session 按角色调用的能力单元。**

---

## 最小落地模板

### 主 session 先做

- 目标重述
- 当前瓶颈判断
- 选择角色
- 定义每个角色的输入 / 输出

### 再派出去

- Planner：拆
- Builder：做
- Reviewer：验

### 再回收

- 主 session 汇总
- 做最终判断
- 决定下一轮

---

## 一句话总结

Agent Team 不是“多开几个 agent 就行”，而是：

> **主 session 持有总控权，其他 agent 按角色被调度，结果再统一回流到主 session 做最终判断。**
