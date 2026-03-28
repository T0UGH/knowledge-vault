---
title: 三篇 Agent Harness 文章总结
date: 2026-03-28
tags:
  - ai
  - agents
  - harness
  - openai
  - anthropic
  - summary
---

# 三篇 Agent Harness 文章总结

## 范围

今天串读并讨论了三篇核心文章：

1. Anthropic - *Effective harnesses for long-running agents*  
2. Anthropic - *Harness design for long-running apps*  
3. OpenAI - *工程技术：在智能体优先的世界中利用 Codex* / *Harness engineering*

这三篇并不是彼此孤立的，而是刚好拼成了一套更完整的方法论。

---

## 总结一句话

> AI 时代真正的工程杠杆，不在“让模型更努力”，而在“把环境、记录、边界、反馈回路设计成让 agent 能持续、可控、可验收地工作”。

---

## 第一篇贡献：长任务的核心是状态交接

Anthropic 第一篇最重要的点是：

- 长任务 agent 最大问题不是不会做
- 而是 **跨 session 会断片**
- 所以必须有：
  - progress file
  - feature list
  - clean state
  - verification

### 核心原则

> 长任务首先是状态管理问题，不只是推理能力问题。

### 直接启发

- 多轮任务不能只靠聊天记录
- 要有轻量状态文件
- 要回答：
  - 当前做到哪了
  - 最近做了什么
  - 下一步是什么
  - 什么算完成

---

## 第二篇贡献：不要让执行者当自己的裁判

Anthropic 第二篇把问题进一步推进：

- self-evaluation 天然偏宽松
- generator 和 evaluator 要分开
- planner / generator / evaluator 是健康职责拆分
- sprint contract 很重要：先谈清楚这一轮的 done，再开写

### 核心原则

> 复杂任务的关键不是让一个 agent 更万能，而是让多个角色各自守住自己的职责边界。

### 直接启发

- 执行和验收要分开
- spec 和本轮 contract 要分开
- evaluator 不是装饰，而是边界任务里的关键增益器
- harness 组件不是越多越好，而是要识别哪些还在真正承重

---

## 第三篇贡献：代码仓库要成为 agent 的记录系统

OpenAI 这篇最牛的地方，是把前两篇“任务级问题”继续推进成了“仓库级问题”。

它真正讲的是：

- 对 agent 来说，看不见就等于不存在
- `AGENTS.md` 不该是百科全书，而应该是地图
- docs / plans / decisions / tech debt 都应该进入 versioned repo
- 目标是 **agent readability**
- 不靠口头规范，而靠 linter / CI / structural tests 去强制执行不变量

### 核心原则

> agent 时代，真正重要的不是“多写说明书”，而是把信息组织成 agent 能稳定读取、验证、继承、执行的记录系统。

### 直接启发

- repo 不只是代码仓
- 还是：
  - 文档系统
  - 计划系统
  - 决策系统
  - 规则系统
- 规则不能只写出来，还要尽量机械化执行

---

## 三篇拼起来的完整图景

这三篇合起来，可以看成三层：

### 第一层：任务怎么不断片

来自 Anthropic 第一篇：

- progress
- feature list
- clean state
- verification

### 第二层：执行与评审怎么拆

来自 Anthropic 第二篇：

- planner
- generator
- evaluator
- sprint contract
- 角色分工

### 第三层：知识和规则怎么沉淀成系统

来自 OpenAI：

- repo as system of record
- short AGENTS.md as map
- progressive disclosure
- agent readability
- linter / CI / doc gardening / architecture invariants

---

## 最该留下的 6 个判断

### 1. 状态管理比单次推理更重要

长任务失败，很多时候不是模型不会，而是：

- 接不上
- 不知道做到哪
- 恢复局面成本太高

### 2. 没有验收闭环，就没有真正完成

- 写了，不等于完成
- 看起来能用，不等于完成
- **验证通过** 才算完成

### 3. 自评天然偏宽松，执行和裁判必须分开

这不是设计问题的特例，而是通用问题。

### 4. 计划、进度、决策都应该是一等工件

- 不是临时聊天
- 不是脑内记忆
- 而是可落盘、可版本化、可交接、可回溯

### 5. `AGENTS.md` 应该是地图，不是百科全书

真正的 source of truth 应该分布在结构化 docs / plans / references 里。

### 6. 规则最强的形态不是“写出来”，而是“自动执行”

最成熟的系统不是规则很多，而是：

- 规则变成目录结构
- 规则变成状态模板
- 规则变成 linter / CI / structural tests

---

## 对当前协作的直接含义

### 对贵平个人职业升级

这三篇共同指向：

> 更值钱的角色，不再是“亲自写代码的人”，而是“定义问题、设计边界、建立反馈回路、验收结果的人”。

### 对 MT × 贵平 协作模式

后续应该继续强化：

- 多轮任务要有状态文件
- 没有验证证据，不轻易说完成
- 执行和验收要分层
- 规则要从 markdown 逐步长成自动约束

### 对未来系统建设

如果后面要把 OpenClaw / HappyClaw / Hex / knowledge-vault 做成高质量协作体系，核心不是堆更多 agent，而是：

- 记录系统
- 状态交接
- 验收闭环
- 机械化约束

---

## 一句话最后总结

> 这三篇文章共同在说：AI 时代的软件工程，不再是“人写代码，机器辅助”，而是“人定义边界和反馈回路，agent 在结构化记录系统中持续执行”。
