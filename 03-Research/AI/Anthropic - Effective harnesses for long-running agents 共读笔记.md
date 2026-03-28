---
title: Anthropic - Effective harnesses for long-running agents 共读笔记
date: 2026-03-28
tags:
  - anthropic
  - agents
  - harness
  - reading-notes
  - ai
aliases:
  - Effective harnesses for long-running agents 共读笔记
---

# Anthropic - Effective harnesses for long-running agents 共读笔记

原文：[[Anthropic - Effective harnesses for long-running agents]]

## 第一部分（开头三段）中文译文

### 第 1 段

随着 AI agent 能力越来越强，开发者开始越来越多地让它们承担复杂任务，这些任务往往会持续数小时，甚至数天。
但一个尚未被真正解决的问题是：**如何让 agent 在多个 context window 之间持续、稳定地推进工作。**

### 第 2 段

长时间运行的 agent 的核心挑战在于：它们必须在**离散的 session** 中工作，而每个新 session 开始时，都**不记得之前发生过什么**。
你可以想象一个软件项目由轮班工程师负责，但每个新来的工程师都完全不知道上一个班次干了什么。
由于 context window 有限制，而大多数复杂项目又不可能在一个窗口里做完，所以 agent 需要一种机制，来弥合不同 coding session 之间的断层。

### 第 3 段

我们提出了一个两段式方案，让 Claude Agent SDK 能在多个 context window 之间更有效地工作：

1. 一个 **initializer agent**，在首次运行时负责把环境搭好  
2. 一个 **coding agent**，负责在每个 session 中做增量推进，同时为下一个 session 留下清晰的交接产物

配套的 quickstart 里可以看到代码示例。
