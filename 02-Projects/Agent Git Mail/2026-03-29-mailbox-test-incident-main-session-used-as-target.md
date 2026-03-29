---
title: Mailbox 测试事故：错误使用主会话作为注入目标
date: 2026-03-29
tags:
  - agent-git-mail
  - incident
  - testing
  - openclaw
status: active
---

# Mailbox 测试事故：错误使用主会话作为注入目标

## 事故结论

本次 `Agent Git Mail` / OpenClaw 集成验证中，错误地使用了 **当前正在服务的主会话** 作为 mailbox `inject / wake` 的真实测试目标。

这是一次 **测试隔离失败** 事故，不是普通的小失误。

---

## 错误点

错误做法：

- 使用 MT 当前正在服务用户的飞书主会话
- 作为 forced `sessionKey` 测试目标
- 对该主会话执行真实的新信注入 / wake 验证

这等于把生产中的主 agent 会话当成了被测对象。

---

## 影响

已知影响：

- MT 出现宕机，约一小时不可用
- Hex 后续进行了多次重启，才把 MT 救回来
- 验证虽然观察到了提醒，但验证方式本身不可接受

---

## 根因判断

根因不在 plugin 代码细节，而在更上层的测试方法：

> **生产会话与测试会话没有隔离。**

本次违反了一个必须收死的原则：

> **不能拿当前主会话做 mailbox / inject / wake 类测试。**

---

## 后续硬规则

从这次事故开始，后续必须遵守以下规则：

1. **禁止使用当前主会话作为 mailbox / inject / wake 的测试目标**
2. **所有注入类验证必须使用隔离测试会话**
3. **执行测试的 agent、接收注入的 agent、结果判断者，默认不能是同一个正在服务的主 agent**
4. **若验证行为可能影响当前 agent 可用性，则默认必须放入隔离环境（测试 session / 独立 OpenClaw / Docker / 独立 agent）**

---

## 对验证方案的修正要求

后续 forced sessionKey 验证不应再使用当前飞书主会话。

更合理的方向：

- 单独测试 session
- 单独测试 agent
- 独立 OpenClaw 测试实例
- 必要时使用 Docker 隔离环境

---

## 一句话总结

> **这次问题不是“功能没测通”，而是“测试方式把生产主会话当成了靶子”。**
