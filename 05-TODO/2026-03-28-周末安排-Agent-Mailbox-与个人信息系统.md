---
title: 2026-03-28 周末安排：Agent Mailbox 与个人信息系统
date: 2026-03-28
tags:
  - planning
  - weekend
  - agent-mailbox
  - obsidian
status: active
---

# 这周六/周日日程安排

## 周六（2026-03-28）

### 1. Agent Mailbox：重设计 inject 消息链路
- [ ] 和 [[MT]] 重新设计 Agent Mailbox
- [ ] 明确方向：**inject 消息功能替代 heartbeat 通知**
- [ ] 收口目标：明确新协议、边界、宿主注入方式、状态流转
- [ ] 和 [[Hex]] 对齐施工范围
- [ ] 输出一版可执行的实现清单

### 2. MT 环境补齐
- [ ] 给 [[MT]] 安装 opencli
- [ ] 给 [[MT]] 安装 feishucli

### 3. skills 调研
- [ ] 找其他有趣的 skills
- [ ] 调研 `supermemory` 是做什么的
- [ ] 记录：适用场景、是否值得接入、和当前工作流的关系

---

## 周日（2026-03-29）

### 1. Rook：个人信息收集系统设计讨论
- [ ] 和 [[Rook]] 重新讨论个人信息收集系统设计
- [ ] 明确多个源的信息采集方式
- [ ] 明确归档到 Obsidian 仓库的结构与入口
- [ ] 明确周期性产出链路：**Obsidian 仓库 -> 微信公众号 / 小红书**
- [ ] 输出一版系统边界：采集、筛选、沉淀、再生产

### 2. Hex 施工推进
- [ ] 跟进 [[Hex]] 完成 Agent Mailbox inject 方案施工
- [ ] 做一次结果验收：实现是否真的摆脱 heartbeat 通知路径

### 3. Rook 环境补齐
- [ ] 给 [[Rook]] 安装 feishucli

---

## 关键产出
- [ ] Agent Mailbox inject 方案收口
- [ ] Hex 施工完毕
- [ ] 个人信息收集系统设计收口
- [ ] MT / Rook 环境补齐
- [ ] supermemory 调研结论

## 提醒
- [ ] 2026-03-29 08:00 提醒我检查周末安排进度
- [ ] 2026-03-30 08:00 提醒我回顾周末产出与后续动作

> [!note]
> 这份 note 只是计划记录。若需要“准点自动提醒”，还需要额外接一个真正的提醒机制（例如日历、cron、消息注入、或别的调度器）。
