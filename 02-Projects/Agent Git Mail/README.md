---
title: Agent Git Mail
tags:
  - agm
  - agent-git-mail
  - project-index
  - mailbox
aliases:
  - AGM
  - Agent Git Mail 项目索引
---

# Agent Git Mail

## Included
- AGM 项目主线设计、实现计划、阶段收口与集成判断
- 与 OpenClaw / HappyClaw / activator / daemon / profile 模型相关的高价值项目笔记
- 少量与 AGM 强相关的内容定位/对外表达草稿

## Current View
- AGM 已完成 **1.0 contract closure** 与 **v1.1.0 / v1.1.1 阶段收口**
- 当前更适合把 AGM 理解为：**Git-backed async mailbox transport + wakeup integration layer**
- 已经收过的重点方向包括：
  - profile 模型
  - daemon lifecycle / launchd
  - external activator
  - HappyClaw / OpenClaw integration
  - doctor / contract / observability

## Entry Notes

### 1. 项目总览 / 对外定位
- [[2026-03-29-agent-git-mail-one-pager]]
- [[2026-03-29-agent-git-mail-v0-formal-design]]
- [[2026-03-29-agent-git-mail-openclaw-feasibility]]
- [[2026-04-05-agm-v1.1.0-release-note]]

### 2. 当前最值得先读
如果只想快速进入当前状态，建议从这几篇开始：
- [[2026-04-05-agm-gap-analysis]]
- [[2026-04-05-agm-1.0-contract-closure-note]]
- [[2026-04-04-agm-next-step-handoff]]
- [[2026-04-06-rook-agm-install-guide]]

### 3. 架构主线
- [[2026-04-05-agm-1.0-contract-design]]
- [[2026-04-05-agm-1.0-polish-design]]
- [[2026-04-04-agm-profile-model-design]]
- [[2026-04-04-agm-daemon-lifecycle-launchd-design]]
- [[2026-04-03-agm-happyclaw-mailbox-integration-redesign]]

### 4. 实现计划 / 落地稿
- [[2026-04-05-agm-1.0-contract-closure-implementation-plan-final]]
- [[2026-04-05-agm-1.0-contract-closure-implementation-plan]]
- [[2026-04-04-agm-daemon-lifecycle-launchd-implementation-plan-final]]
- [[2026-04-04-agm-profile-model-implementation-plan-final]]
- [[2026-04-04-agm-profile-model-implementation-plan-mt]]
- [[2026-04-03-agm-happyclaw-integration-implementation-plan-final]]
- [[2026-04-03-agm-happyclaw-integration-implementation-plan-mt]]

### 5. 集成 / Runtime 方向
- [[2026-04-02-agm-openclaw-integration-progress]]
- [[2026-04-01-agm-happyclaw-integration-design-phase1]]
- [[2026-04-02-agm-external-activator-final-design]]
- [[2026-04-02-agm-external-activator-validation-direction]]
- [[2026-04-02-agm-feishu-system-message-handling-gap]]
- [[2026-03-30-agm-plugin-feishu-notification-option]]

### 6. 验证 / 事故 / 经验
- [[2026-03-29-mailbox-test-incident-main-session-used-as-target]]
- [[2026-03-29-openclaw-plugin-forced-sessionkey-test]]
- [[2026-03-29-openclaw-plugin-minimal-verification-plan]]
- [[2026-03-29-agm-docker-build-fix]]
- [[2026-03-30-agm-openclaw-plugin-docker-verification-progress]]
- [[2026-04-04-agm-profile-docker-e2e-design]]
- [[2026-03-31-agm-review-v1-config-and-next-round]]

### 7. 较早阶段的策略 / README / 内容定位
- [[2026-03-29-agent-git-mail-strategy-notes]]
- [[2026-03-30-agm-bootstrap-implementation-direction]]
- [[2026-03-30-agm-mvp-supplement-thinking]]
- [[2026-03-29-readme-opening-brainstorm]]
- [[2026-03-29-readme-opening-v2]]
- [[2026-03-29-readme-front-half-notes]]
- [[2026-03-29-ai-monetization-direction-notes]]
- [[2026-03-29-wechat-positioning-notes]]
- [[2026-03-29-wechat-first-10-topics]]

## Suggested Reading Paths

### 路径 A：快速了解 AGM 现在是什么
1. [[2026-03-29-agent-git-mail-one-pager]]
2. [[2026-04-05-agm-1.0-contract-design]]
3. [[2026-04-05-agm-1.0-contract-closure-note]]
4. [[2026-04-05-agm-v1.1.0-release-note]]

### 路径 B：想看工程收口是怎么完成的
1. [[2026-04-04-agm-profile-model-design]]
2. [[2026-04-04-agm-profile-model-implementation-plan-final]]
3. [[2026-04-04-agm-daemon-lifecycle-launchd-design]]
4. [[2026-04-04-agm-daemon-lifecycle-launchd-implementation-plan-final]]
5. [[2026-04-05-agm-1.0-contract-closure-implementation-plan-final]]

### 路径 C：想接手安装 / 运维 / 使用
1. [[2026-04-06-rook-agm-install-guide]]
2. [[2026-04-04-agm-next-step-handoff]]
3. [[2026-03-29-agm-docker-build-fix]]
4. [[2026-03-31-agm-review-v1-config-and-next-round]]

## Boundary
- 这里放 **AGM 项目主线笔记**，不是所有 mailbox / OpenClaw 笔记的总垃圾桶
- 更偏通用的 agent 架构判断，应优先放到 [[03-Research/Agent-Systems]] 或相关 Research 目录
- 更偏 OpenClaw 宿主本体的问题，应优先放到 [[02-Projects/OpenClaw]]
