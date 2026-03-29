---
title: Agent Git Mail README 前半部分收口记录
date: 2026-03-29
tags:
  - agent
  - readme
status: draft
---

# Agent Git Mail README 前半部分收口记录

## 当前判断

README 当前最值得花脑力的部分，不是结构、索引、文档列表，而是：

1. opening 的几句话
2. 核心模型段落
3. opening 后面的 4 个核心 bullet

这些内容决定项目的认知锚点和辨识度。

---

## 当前 opening 定稿方向

```md
# Agent Git Mail

一个极简、git-native 的 agent 异步邮箱系统，把协作重新收缩回“写信”这件事。

**Simple is better.**
**No server. No task system. No orchestration maze.**

每个 agent 一个 git repo；仓库就是它的邮箱。

这不是工作型 agent 的任务总线。
它服务于 OpenClaw 这类长期在线、持续协作的助理型 agent，而不是 Claude Code、Codex 这类“干完就走”的工作型 agent。
```

### 关键判断

- 把“异步通信系统”改成 **“异步邮箱系统”**，更贴项目心智
- `Simple is better.` 后面不再加中文解释，直接接三连英文立场句
- “这不是工作型 agent 的任务总线。” 单独成句，作为锤子句
- README 必须明确项目服务的是 **助理型 agent**，而不是工作型 agent

---

## 核心模型段落定稿方向

```md
Agent Git Mail 的核心很简单：每个 agent 一个 git repo，仓库就是它的邮箱。
一封信就是一个普通 Markdown 文件，由 frontmatter 和正文组成；文件名就是主标识，`reply_to` 直接引用文件名。
一个很薄的 daemon 只负责发现新信并提醒 agent，不承担中心化的编排和调度。
```

### 关键判断

- 这段不再追求锋利，而是追求清楚、稳、可理解
- opening 负责立场，核心模型段落负责把事情讲明白
- 这段的职责是把“repo = mailbox / file = mail / daemon = thin notifier”讲清楚

---

## opening 后的 4 个核心 bullet

```md
- **每个 agent 一个 git repo。** 仓库就是它的邮箱。
- **一封信就是一个 Markdown 文件。** frontmatter + 正文，就是完整协议。
- **文件名就是主标识。** `reply_to` 直接引用文件名。
- **daemon 只是邮差。** 它只发现新信并提醒 agent，不做中心化编排和调度。
```

### 关键判断

- 这 4 条的目标不是协议穷举，而是让读者在 10 秒内建立正确心智模型
- “daemon 只是邮差”这一条保留画面感和记忆点

---

## 当前下一步

README 后面的“怎么用”，不应先按纯 `agm` CLI 工具来写，而应优先探索：

> **在 OpenClaw 里怎么安装、怎么启用、怎么形成最小闭环。**

也就是说，后续应优先澄清：

- `agm` 如何分发（当前倾向 npm 包）
- OpenClaw plugin 的真实安装/启用路径
- README 的 Quick Start 最终应以哪条安装路径为主
