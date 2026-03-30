---
title: OpenClash 配置问题
category: project
tags:
  - OpenClash
  - Clash
  - DNS
  - fake-ip
  - redir-host
  - Claude Code
  - Codex
summary: 围绕家用网络下 OpenClash 的 DNS、防泄漏、AI 服务分流和配置调优建立的长期项目入口。
---

# OpenClash 配置问题

## 项目目标

围绕家用网络下的 OpenClash 配置，持续解决这些问题：

- DNS 泄漏风险
- fake-ip / redir-host 取舍
- TUN 是否需要开启
- Claude / OpenAI / Codex 等 AI 服务的显式分流优化
- 配置变更的可回滚与可验证

## 当前结论

- 家用路由器 OpenClash 场景下，默认**先不开 TUN**。
- 当前最关键的问题不是节点，而是 **DNS 配置闭环是否成立**。
- 对于担心 DNS 泄漏的场景，当前方向应优先考虑：
  - `dns.enable: true`
  - 优先尝试 `enhanced-mode: fake-ip`
  - 补齐基础 `fake-ip-filter`
- 现有规则对 Claude / OpenAI / Codex 大概率能通过 `MATCH,极连云` 兜底代理，但还不是 AI 专项优化型配置。

## 当前文件

- 原始配置：`/Users/haha/.openclaw/workspace/j.yaml`
- 副本配置：`/Users/haha/.openclaw/workspace/rook-clash.yaml`
- 当前诊断：`diagnosis-2026-03-30.md`

## 下一步候选

- 补一版 AI 专项显式规则（Claude / OpenAI / Codex）
- 继续收紧 DNS 配置与 fallback 行为
- 验证 fake-ip 开启后的实际表现与副作用
- 形成一版“家用 OpenClash for AI”的稳定模板
