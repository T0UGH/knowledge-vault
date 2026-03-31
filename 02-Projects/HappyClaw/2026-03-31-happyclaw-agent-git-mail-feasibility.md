---
title: HappyClaw × Agent Git Mail 集成可行性判断（第一轮）
date: 2026-03-31
tags:
  - happyclaw
  - agent-git-mail
  - mailbox
  - integration
  - feasibility
status: in-progress
summary: 第一轮调研结论：HappyClaw 即使没有像 OpenClaw 那样的 plugin/runtime 扩展面，仍然可以在 fork 内以“最小内建集成”方式打通 Agent Git Mail。关键原因是 HappyClaw 已经具备 /internal/inject、活跃 runner 直接注入、无活跃 runner 排队/唤醒等底座能力。第一阶段不建议先做通用 plugin 框架，而应直接在 HappyClaw 主进程内补 mailbox watcher/service，目标对齐 OpenClaw 的 4 项核心能力。
---

# HappyClaw × Agent Git Mail 集成可行性判断（第一轮）

## 结论

**技术上可行，而且推荐做。**

HappyClaw 即使没有像 OpenClaw 那样成熟的 plugin/runtime 扩展面，也已经具备做这件事的关键底座：

- 有内部注入入口：`POST /internal/inject`
- 有主会话消息存储与前端广播链路
- 有活跃 runner 直接注入能力
- 有无活跃 runner 时的排队/唤醒能力
- 有“只注入主会话”的边界约束

所以第一阶段**不需要先补一套 plugin 系统**，也能实现和 OpenClaw 接近的 AGM 功能闭环。

## 当前阶段的目标收口

贵平已明确第一阶段目标是：**和 OpenClaw 一样的核心功能都要**，至少包含：

1. 新信检测后，能把提醒注入主会话可见
2. 如果 agent 正在跑，能直接 IPC 注入继续处理
3. 如果 agent 没在跑，能排队/唤醒，等它起来再处理
4. 有 agent ↔ mailbox repo 的配置与轮询机制

这意味着第一阶段不是“做一个提醒 demo”，而是要打通**最小完整集成闭环**。

## 调研结果

## 1. HappyClaw 目前不像 OpenClaw 那样依赖 plugin 扩展面

从代码结构看，HappyClaw 当前没有发现类似 OpenClaw 那样的通用 plugin runtime / registerService / hook API 作为正式扩展入口。

所以如果目标是尽快打通 AGM，第一阶段不应先去补“HappyClaw 通用插件框架”。

原因：
- 成本大
- 设计空间容易发散
- 会把一个集成问题膨胀成平台化问题

## 2. 但 HappyClaw 已经有一条非常关键的注入链路

已经存在：
- `src/routes/inject.ts`
- `src/web.ts` 中挂载 `/internal`
- 设计文档：`docs/design-mailbox-injection.md`

`/internal/inject` 的能力不是停留在纸面上，而是已经实际存在于代码中。

它当前可以完成：
- 外部请求进入 HappyClaw
- 写入 DB
- 广播到前端聊天界面
- 尝试注入活跃 runner
- 如果没有活跃 runner，则排队等待处理

这条链路的意义非常大：

> AGM 集成最关键的“把新信转成主会话事件”这一步，底座已经存在。

## 3. 活跃 runner 直接注入与无活跃 runner 排队都已有现成模式

`inject.ts` 当前逻辑已经体现了两类分支：

### 情况 A：当前有活跃 runner
通过：
- `deps.queue.sendMessage(...)`

如果返回：
- `sent`
- `interrupted_stop`
- `interrupted_correction`

都可视为已经把消息写进 agent 的处理链路。

### 情况 B：当前无活跃 runner
通过：
- `deps.queue.enqueueMessageCheck(chatJid)`

进入排队/唤醒路径，后续再由 HappyClaw 自己拉起处理。

这恰好对应 AGM 在 OpenClaw 上想要的两种行为：
- 正在运行时直接继续处理
- 没在运行时先提醒/唤醒再处理

## 4. 主会话 only 的边界与 mailbox 设计一致

在 `inject.ts` 中已经看到一条很重要的约束：

- 只允许注入 `is_home` 的主会话

这和 mailbox / AGM 当前阶段的边界高度一致：

> mailbox 必须注入主会话，不能乱打进独立 thread / 非 home 会话。

这说明 HappyClaw 当前实现方向与 AGM 的集成原则并不冲突。

## 第一阶段推荐方案

## 推荐：A-minus 方案

即：

> **不造 plugin 系统，直接在 HappyClaw fork 内补一个最小 mailbox watcher/service，复用现有 `/internal/inject` 完成 AGM 集成闭环。**

### 为什么不是先做 sidecar
sidecar 当然可做，但第一阶段不是最佳默认。

第一阶段目标是：
- 少部署面
- 少心智负担
- 先把最小闭环打通

如果做 sidecar，会引入：
- 另一进程生命周期
- 另一份日志
- 另一层配置
- 排障路径更长

对于第一阶段来说，这些都不划算。

### 为什么不是先做通用 plugin framework
原因更明确：
- 目标是 AGM 集成，不是 HappyClaw 平台化
- 做 framework 会明显扩大 scope
- 这是第二阶段甚至更后的事情

## 推荐架构

```text
HappyClaw
├─ agm-integration/
│  ├─ config loader
│  ├─ repo watcher / poller
│  ├─ waterline store
│  ├─ new-mail detector
│  └─ inject adapter
│
└─ existing runtime
   ├─ /internal/inject
   ├─ queue.sendMessage
   ├─ enqueueMessageCheck
   └─ main-session chat pipeline
```

这套架构的关键原则是：

> HappyClaw 是宿主，不是 mailbox 领域模型本身。

也就是说：
- mailbox watcher 负责发现新信
- inject adapter 负责把它转成 HappyClaw 可消费事件
- HappyClaw 继续只负责“消息进入主会话并被 agent 处理”
- 不把 mailbox 复杂业务协议塞进 HappyClaw 核心状态机

## 第一阶段要补的能力

真正需要新增的，不是注入链路，而是 AGM 集成层：

1. **mailbox 配置模型**
   - self / contacts / poll interval
   - agent ↔ repo 的寻址关系

2. **repo watcher / poller**
   - 定时 pull / fetch
   - 判定新增 inbox 文件

3. **waterline / 新信判定**
   - 首次启动不补历史
   - 后续只识别新增 inbox 文件
   - 避免重复报旧信

4. **HappyClaw 内的 service 生命周期**
   - 启动时拉起 poller
   - 关闭时停止
   - 与现有 web/server/runtime 生命周期挂接

5. **状态与诊断面**
   - 基本日志
   - 当前 watcher 是否活着
   - 最近检测到什么新信
   - 当前 repo path / poll interval 是什么

## 建议沿用的配置语义

第一阶段配置不要重新发明另一套模型。
建议直接沿用 AGM 当前更合理的目标模型：

```yaml
self:
  id: mt
  repo_path: /path/to/my-mailbox

contacts:
  hex:
    repo_path: /path/to/hex-mailbox

notifications:
  default_target: main

runtime:
  poll_interval_seconds: 30
```

说明：
- `self` 负责表达“我是谁 / 我的 mailbox 在哪”
- `contacts` 负责表达“别人是谁 / 别人的 mailbox 在哪”
- 对收信检测来说，第一阶段严格必须的是 `self`
- 但如果目标是和 AGM 运行时模型对齐，`contacts` 最好第一阶段一起保留

## 对齐 OpenClaw 的 4 项能力：逐项判断

## 1. 新信检测后主会话可见
**可行。**

路径：
- watcher 检测到新信
- 转成结构化文本
- 调用 `/internal/inject`
- HappyClaw 写 DB + 广播前端

结果：用户能在主会话里直接看到这条 mailbox 提醒。

## 2. agent 正在跑时，直接 IPC 注入继续处理
**可行。**

复用：
- `deps.queue.sendMessage(...)`

当返回 `sent` 或 `interrupted_*` 时，可视为已经成功进入当前 agent 的处理流。

## 3. agent 不在跑时，排队/唤醒
**可行。**

复用：
- `deps.queue.enqueueMessageCheck(chatJid)`

这意味着即使当前没有活跃 runner，也可以把 mailbox 事件转成“后续会被处理”的状态。

## 4. agent ↔ mailbox repo 配置与轮询机制
**需要新增，但可做。**

这是第一阶段真正的主要开发量，但属于比较标准的 watcher / polling / state 问题，不是架构 blocker。

## 技术风险

## 风险 1：HappyClaw 没有 OpenClaw 那样天然的后台 service 框架
这不是 blocker。

可选做法：
- 在 `startWebServer` / `index.ts` 生命周期中启动 AGM poller
- 或复用现有 scheduler/task 风格挂进去

关键是：
- 先做最小稳定 service
- 不先设计通用 plugin framework

## 风险 2：配置语义再次漂移
这在 AGM 那边已经踩过一次。

所以 HappyClaw 集成时要避免：
- 再造一套新的配置模型
- 把 bootstrap 阶段语义误当成正式运行时语义

要坚持：
- `self + contacts` 才是正式运行时配置语义

## 风险 3：注入内容格式不稳定
`/internal/inject` 目前注入的是普通文本。

这没问题，但需要定义 mailbox 注入文本格式，否则 agent 看到的只是一条普通消息，不利于稳定理解。

建议后续专门定义：
- sender
- reason
- message-id
- subject
- requires-reply
- next action hint

## 风险 4：重复处理 / cursor 管理
HappyClaw 注入路径本身已经有 cursor 推进逻辑。

真正要小心的是：
- watcher 自己的 waterline 如何维护
- 首次启动如何不补历史
- 重启后如何不重复报旧信

这个问题是可控的，但需要设计时先定清楚。

## 第一阶段边界

### 要做
- mailbox watcher/service
- HappyClaw 配置接入
- 新信检测 → inject
- 活跃 runner 直送
- 无活跃 runner 排队/唤醒
- 主会话 only
- 基本日志与状态

### 不做
- 通用 plugin framework
- 花哨 UI / 特殊样式
- mailbox obligation / priority / 决策层
- 完整 mailbox 控制台
- 多 target session 路由策略

## 当前推荐判断

一句话总结：

> HappyClaw 即使没有 OpenClaw 那样的 plugin 面，也完全可以在 fork 内通过“最小内建 mailbox watcher + 复用 `/internal/inject`”实现和 OpenClaw 接近的 AGM 能力闭环。

因此，第一阶段**推荐做**，而且推荐：

> **直接放进 HappyClaw 主进程内做最小集成，不先拆 sidecar，不先做 plugin framework。**

## 下一步

下一步应进入正式方案设计，至少回答清楚：

1. watcher/service 挂在哪个生命周期点
2. waterline 状态落在哪里
3. 配置文件如何组织
4. mailbox 注入文本格式如何定义
5. 诊断与验证链路怎么做

当前阶段已完成：**第一轮技术可行性调研与方向选择**。
