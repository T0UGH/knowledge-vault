---
title: QA 角色更适合异构类 Claw 系统，而不是 Subagent
date: 2026-04-05
tags:
  - agent
  - qa
  - architecture
  - openclaw
  - hermes
status: active
aliases:
  - QA 角色应该是 subagent 还是异构系统
  - QA role architecture decision
---

# QA 角色更适合异构类 Claw 系统，而不是 Subagent

## 结论

**QA 角色更适合做一个异构的、类 Claw 的系统，而不适合只做 OpenClaw 风格的 subagent。**

一句话原因：

> **QA 的价值在“第二视角 + 独立判断 + 独立会话面”，而 subagent 天然更像主控下面的托管执行单元。**

---

## 为什么不推荐 QA 只做 subagent

OpenClaw 风格的 subagent 设计，本质上是：

- 父会话创建
- 父会话约束
- 结果回流给父会话
- 父会话负责最终收口

这很适合：

- coding
- research 子任务
- 并发执行
- 资料整理

但不太适合：

- 审核主控判断
- 做相对独立的 QA / review partner
- 充当 MT backup

原因不是 subagent 不强，而是它的默认角色更像：

> **被托管的手脚**

而不是：

> **能提出第二意见的独立脑**

---

## QA 角色真正需要的能力

一个有价值的 QA agent，核心不是“再多一个会执行的 agent”，而是：

- **独立判断**
- **独立上下文**
- **独立会话面**
- **长期积累自己的 review / 验收视角**
- **必要时敢于和主控结论不一致**

它的任务不是简单响应主控指令，而是补上系统中最稀缺的视角：

- 验收视角
- 挑错视角
- 补盲视角
- 风险识别视角

---

## 为什么异构类 Claw 系统更适合 QA

### 1. 独立上下文更有利于形成“第二视角”

如果 QA 只是 subagent，它很容易顺着主控的上下文往下推演。

但 QA 真正要有价值，反而需要保留自己的：

- session
- memory
- review 风格
- 关注点
- 输出模板

这样它看 GitHub 项目、看方案、看 MT 产物时，才更容易保留“第二意见”。

### 2. 更像真正的 QA，而不是主控延伸

真正有价值的 QA，不是重复主控思路，而是能说：

- 这个判断我不同意
- 这里验收不够
- 这里像样子货
- 这里风险被低估了
- 这里值得继续，那里不值得投入

如果只是 subagent，它很容易退化成：

> “主控让我看什么，我就顺着看什么。”

### 3. 更适合做 MT backup

当前规划里，第三位不只是 QA，还承担：

- MT backup
- GitHub 项目观察员
- MT 忙时的第一轮筛查者

这些工作更像：

- 独立巡检
- 独立筛选
- 独立输出建议

而不是一次性的 delegated subtask。

---

## 推荐分工

### MT

- 主控
- 架构判断
- 编排
- 最终拍板

### Hex

- coding agent
- 救生员
- 修复 MT 和第三位留下的问题
- 工程执行与验证

### 第三位（QA）

- QA / review agent
- GitHub 项目筛查
- 第二视角
- MT backup
- 独立输出 accept / revise / reject / risk judgment

---

## 更准确的架构判断

**QA 最适合的形态不是“subagent”，而是：**

> **异构独立系统 + 可被 MT 调用**

也就是：

- 平时它是独立 QA agent
- 有自己的会话和记忆
- MT 可以把任务投递给它
- 它输出 review / accept / reject / risk
- 必要时再让 Hex 负责修

这比“把 QA 完全塞进 MT 的 subagent 树”更合理。

---

## 现实落地建议

如果当前资源有限，不一定一步到位做完整异构系统。

可以分阶段：

### 短期

先让 **Hermes** 这类长期 agent 形态扮演 QA。

### 中期

把它的：

- 输出模板
- 职责边界
- 会话面
- review 纪律

逐步稳定下来。

### 后期

再决定是否演化成更完整的“类 Claw QA 系统”。

---

## 最终拍板

### 不推荐

- 让 QA 主要作为 OpenClaw subagent 存在

### 推荐

- 让 QA 成为一个异构、类 Claw 的独立系统
- 但保持可被 MT 调用、可接入当前协作链路

一句话总结：

> **QA 不是多一只手，而是多一个脑。**
