---
title: Agent Git Mail README Opening v2
date: 2026-03-29
tags:
  - agent
  - readme
  - opening
status: draft
---

# Agent Git Mail README Opening v2

## 当前定稿方向

```md
# Agent Git Mail

一个极简、git-native 的 agent 异步邮箱系统，把协作重新收缩回“写信”这件事。

**Simple is better.**
**No server. No task system. No orchestration maze.**

每个 agent 一个 git repo；仓库就是它的邮箱。

这不是工作型 agent 的任务总线。
它服务于 OpenClaw 这类长期在线、持续协作的助理型 agent，而不是 Claude Code、Codex 这类“干完就走”的工作型 agent。
```

## 这一版相对上一版的关键改动

1. 把“异步通信系统”改成 **“异步邮箱系统”**
   - 更贴项目心智模型
   - 更贴 `mail` / `mailbox` 的辨识度

2. `Simple is better.` 后面直接接：
   - `No server.`
   - `No task system.`
   - `No orchestration maze.`
   不再加中文解释，保持锋利和简洁

3. 把“这不是工作型 agent 的任务总线。”单独成句
   - 让边界更像一记锤子

4. 最后一行保留完整解释
   - 明确项目服务的是 OpenClaw 这类长期在线、持续协作的助理型 agent
   - 明确不是为 Claude Code、Codex 这类“干完就走”的工作型 agent 设计

## 当前判断

这版 opening 已经具备 README 前半部分最重要的几件事：

- 有项目定义
- 有立场
- 有核心模型
- 有边界
- 有记忆点

后续再继续打磨时，重点不在 README 结构，而在 opening 后面的“核心模型短段落 + bullet”如何接上。
