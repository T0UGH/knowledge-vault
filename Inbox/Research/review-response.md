---
title: Hermes 像素办公室看板方案 Review Response
tags:
  - hermes
  - monitoring
  - architecture
  - review
---

# Hermes 像素办公室看板方案 Review Response

关联文件：

- `Inbox/Research/2026-04-12-hermes-pixel-office-monitor-board-v1.md`
- `Inbox/Research/review.md`

## 结论

这轮 Codex review 整体质量高，指出的问题大多不是“方向错了”，而是：

- 抽象边界还不够收紧
- 状态语义还不够正交
- snapshot / delta / ID / 生命周期规则还没写死

我的总体处理原则：

- **P0 级问题基本接受**
- **P1 级问题大多接受**
- **个别建议不在 V1 立即落地，但会保留为后续约束**

一句话拍板：

> 这轮 review 不是推翻方案，而是要求把它从“方向正确的设计稿”收口成“能稳定实现的工程规格”。

## 逐条响应

## 1. Scope drift：Hermes V1 与通用平台混在一起

### 结论
**接受。**

### 原因
这是当前方案里最真实的问题之一。

文档前半段确实更像：
- Hermes-first 方案

后半段逐步升级成：
- 通用 Agent Runtime Monitor Platform

这个方向本身没错，但 Codex 说得对：
当前抽象还没有收缩到最小稳定核心。

### 处理方式
V1 后续应明确区分两层：

#### V1 交付目标
- Hermes adaptor 先跑通
- 协议内核只保留真正已经验证过的对象

#### 平台方向
- 保留 adaptor/protocol 架构
- 但不把所有未来对象都提前推进 v0 核心

### 具体决定
**接受 review 建议，后续将协议核心收缩为：**

- `collector`
- `machine`
- `runtime`
- `activity`
- optional `channel`
- optional `subagent`

以下对象降级处理：

- `workspace` → 先作为 runtime 属性/显示语义，不作为独立核心对象
- `task` → 暂不进入协议核心
- `resource_usage` → 继续拆到 `context_usage` / `token_usage` / `cost_estimate`，不额外保留一个泛对象

## 2. 状态语义混合：liveness / health / activity 没拆开

### 结论
**接受。**

### 原因
这个问题非常关键。

当前方案里确实混用了：
- machine 是否在线
- runtime 是否可连接
- runtime 是否在工作
- 当前 activity 是什么

这会导致：
- UI 优先级不清楚
- adaptor 映射标准不统一
- 不同 runtime_type 难以保持一致

### 处理方式
后续统一拆成 3 层：

#### 1. `reachability_state`
表示可达性/心跳层：
- `online`
- `offline`
- `stale`

#### 2. `work_state`
表示工作态：
- `idle`
- `active`
- `waiting`
- `blocked`
- `error`

#### 3. `activity.kind`
表示“具体正在做什么”：
- `reading`
- `writing`
- `tool_call`
- `waiting_input`
- `waiting_approval`
- ...

### 具体决定
**完全接受，并作为下一轮文档修订的 P0 修改项。**

## 3. Snapshot / delta 一致性规则缺失

### 结论
**接受。**

### 原因
这是当前方案里最大的工程风险之一。

如果不定义：
- collector sequence
- hub state version
- idempotency
- omission semantics

后面极容易出现：
- 幽灵 subagent
- 已结束 channel 不消失
- 前端状态回退
- WS 增量和全量状态不一致

### 处理方式
后续应明确写死：

#### 必加规则
- `collector_seq`：collector 侧单调递增
- `state_version`：hub 当前态版本号
- 幂等键：`collector_id + snapshot_id`
- full snapshot replacement 语义
- omission semantics：对象未出现在新 snapshot 中时怎么办

### 具体决定
**完全接受，并列为 P0 修改项。**

## 4. ID 规则不够硬

### 结论
**接受。**

### 原因
Codex 这里说得很对。

现在文档里很多 ID 还是“生成思路”，不是最终规格。
这会直接影响：
- 去重
- 链接稳定性
- 历史记录
- 详情页路由
- delta merge

### 处理方式
后续文档必须把以下 ID 规则写死：

- `machine_id`
- `collector_id`
- `runtime_id`
- `channel_id`
- `session_id`
- `subagent_id`

并补这些约束：
- 作用域
- 稳定性要求
- rename 后行为
- ephemeral 对象 TTL

### 具体决定
**完全接受，并列为 P0 修改项。**

## 5. 安全边界偏弱

### 结论
**部分接受。**

### 原因
Codex 指出的问题成立：
即使不上传全文，仍然会暴露：
- channel 名
- workspace/profile 名
- model
- command 摘要
- 工作流结构

这确实不是“完全无害”的元数据。

但我不接受把这点直接推导成：
- V1 必须上复杂完整登录系统

因为当前阶段更重要的是：
- 跑通系统
- 收紧元数据外发边界
- 先不要把接入复杂度推高

### 处理方式
V1 保持：
- 长随机 URL
- collector token 强鉴权
- 最小暴露原则

但增加两条补强：

#### 1. 数据外发 allowlist
必须明确“哪些字段允许离开机器”。

#### 2. 命令摘要进一步脱敏
默认不带：
- 绝对路径
- 仓库私有代号
- 原始长参数

### 具体决定
- **接受“安全边界偏弱”的判断**
- **不接受“V1 立即上重鉴权”的隐含方向**
- 采用折中：V1 保持轻访问控制，但强化数据出站约束

## 6. 推断字段应带 confidence / source_quality

### 结论
**接受。**

### 原因
这个建议很值钱。

因为当前这些字段都不是绝对真相：
- `activity`
- `model`
- `subagent`

如果 UI 把这些都当 100% 准确展示，会误导使用者。

### 处理方式
建议为这些字段补：
- `source`
- `confidence`
- 或 `quality`

V1 最简单做法：
- `model_source`
- `activity_source`
- `subagent_source`
- 可选 `confidence: high/medium/low`

### 具体决定
**接受，列为 P1 修改项。**

## 7. Hub normalization 规则要写清

### 结论
**接受。**

### 原因
当前 ingest 是嵌套 snapshot，board query 是平铺状态。
如果不写清：
- 哪边是 canonical
- flatten 时如何处理 absence
- nested 和 flat 的映射关系

实现一定会乱。

### 处理方式
明确：
- ingest shape = collector snapshot
- hub current state = canonical board state
- board API 不直接回原始 snapshot

### 具体决定
**接受，列为 P1 修改项。**

## 8. freshness / smoothing 规则缺失

### 结论
**接受。**

### 原因
这个建议非常工程化，而且和像素办公室 UI 强相关。

没有这些规则：
- runtime 状态会抖
- subagent 一闪而过
- active/waiting 切换会难看

### 处理方式
后续应补：
- offline timeout
- active 窗口时长
- waiting 持续窗口
- subagent 最小显示时长
- 状态 debounce / hysteresis

### 具体决定
**接受，列为 P1 修改项。**

## 9. detail API 需要 recent N / pagination

### 结论
**接受。**

### 原因
这个是明显的边界缺失。
如果不收：
- runtime 详情会越滚越肥
- channel 详情也会失控

### 处理方式
后续明确：
- sessions：recent N
- subagents：recent N
- history：分页或根本不进 V1

### 具体决定
**接受，列为 P1 修改项。**

## 10. metric scope 要统一

### 结论
**接受。**

### 原因
现在文档里有：
- `token_usage`
- `context_usage`
- `cost_estimate`
- `metrics`

但“当前 session / rolling 1h / current snapshot”这些窗口边界还不够硬。

### 处理方式
后续统一要求指标都带：
- `window_scope`

### 具体决定
**接受，列为 P1 修改项。**

## 11. schema evolution / monitor 自监控 / API 收缩

### 结论
**部分接受。**

### 原因
这些都对，但不该现在分散主线。

### 处理方式
#### 接受的部分
- schema evolution 应该在后面补
- monitor system 自身 health 最终必须有

#### 暂不优先的部分
- 现在先不专门写完整自监控章节
- `/board/filters`、`/ingest/events` 是否保留，取决于下一轮是否收缩 API 面

### 具体决定
列为 **P2**，不进当前最小修订集。

## 汇总判断

## 必须马上修（P0）

1. 收缩协议核心对象
2. 状态模型正交化
3. snapshot / delta 一致性规则
4. ID 规则写死
5. 数据出站 allowlist 明确化

## 很快补（P1）

1. 推断字段加 source/confidence
2. hub normalization 规则
3. freshness / debounce / hysteresis 规则
4. recent N / pagination 约束
5. metric scope 统一

## 后面补（P2）

1. schema evolution
2. monitor 自监控
3. 非必要 API 面收缩与治理

## 当前拍板

这轮 review 我给出的总判断是：

> 大方向不改，协议和工程边界收得更硬。

也就是说：
- **接受绝大多数批评**
- **不推翻整体方案**
- **下一步不是重新想产品，而是把规格进一步工程化收口**
