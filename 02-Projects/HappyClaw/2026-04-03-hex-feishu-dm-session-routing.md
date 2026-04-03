---
title: Hex 飞书私聊会话路由结论（2026-04-03）
date: 2026-04-03
tags:
  - happyclaw
  - hex
  - feishu
  - session-routing
status: confirmed
summary: 确认 MT → Hex 的飞书私聊路径已通，无需额外改造；当前飞书私聊 group 已正确注册到 Hex 主会话，后续可默认通过飞书私聊把消息送到 Hex 主会话。
---

# Hex 飞书私聊会话路由结论（2026-04-03）

## 结论

**MT → Hex 的飞书私聊路径已通，无需额外改造。**

当前这条飞书私聊已经正确落到 Hex 主会话：

- 飞书 JID：`feishu:oc_d032da393cc3bdabec385602be187652`
- 主会话：`web:main`
- folder：`main`

也就是说，后续如果目标是：

- 给 Hex 发消息
- 并尽量落在同一个主 session

默认可以优先走 **飞书私聊 Hex**，不必先绕回 agent-bridge 文件通信。

---

## 关键实现判断

Hex 回信给出的核心结论是：

1. 当前「飞书私聊」group 已正确注册到 Hex 主会话
2. HappyClaw 侧会通过 `resolveEffectiveGroup` 找到 sibling home
3. 然后合并执行参数，把消息送入正确的主会话上下文

这说明当前问题的关键已经不是“能不能路由到 Hex”，而是：

> **Hex host 进程是否正在运行。**

---

## 边界 / 限制

### 1. session 连续性依赖 host runtime
这条路径能否保持“同一个 session 的连续感”，核心取决于：

- Hex 对应的 HappyClaw host 进程是否活着
- 主会话 runtime 是否仍在运行

如果 host 不在：
- 路由能力本身不一定错
- 但 session continuity 会断

### 2. 这不是 OpenClaw `sessions_list` 可见性的结论
MT 当前在 OpenClaw 主会话里，并不能直接通过 `sessions_list` 看到 Hex 的 HappyClaw session。

所以这里的结论不是：
- OpenClaw 内部 session 可直接互发

而是：
- **HappyClaw 已经把该飞书私聊正确绑定到 Hex 主会话**
- 因此可通过飞书私聊实现稳定路由

---

## 对后续协作的建议

后续默认策略建议调整为：

1. **优先：飞书私聊 Hex**
   - 目标：尽量落在 Hex 主会话
   - 适用：需要直接找 Hex 协作、追问、续上下文

2. **次选：agent-bridge**
   - 适用：
     - Hex host 不在线
     - 需要留可审计书面任务单
     - 需要异步交接和结构化任务拆解

一句话：

> **实时协作优先走飞书私聊；异步任务交接再走 agent-bridge。**

---

## 来源

Hex 回信文件：

- `inbox/2026-04-03-hex-to-mt-happyclaw-session-routing-analysis.md`
