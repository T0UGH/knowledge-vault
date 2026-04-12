---
title: Hermes 像素办公室监控看板 V1 方案草案
tags:
  - hermes
  - monitoring
  - dashboard
  - agent-systems
  - pixel-art
---

# Hermes 像素办公室监控看板 V1 方案草案

## 结论

建议做一个 **云端中心站 + 各 Mac 主动上报 collector + 像素办公室前端** 的只读监控系统。

V1 不做登录系统，采用：

- **长随机 URL** 作为看板访问入口
- **collector token** 作为机器上报鉴权
- **默认只上传监控元数据，不上传完整对话正文**

视觉层不建议从零做，最合适的路线是：

- **借鉴 `pixel-agents-standalone` / `pixel-agents` 的像素办公室表现层**
- **重写 Hermes 专用的数据采集与状态聚合层**

一句话拍板：

> 借它的办公室可视化壳子，不借它的 Claude transcript 数据接入层。

## 背景与需求收口

当前目标不是做一个传统 Grafana 式监控页，而是做一个 **公网外部可见、带空间感和角色感的 Hermes 监控看板**。

核心诉求包括：

- 多台家用 Mac 上独立运行的 Hermes agent
- 这些机器没有公网 IP
- 一个统一公网页面查看整体状态
- 风格偏《星露谷物语》/像素办公室 / 轻 RPG 叙事感
- 每个 Hermes agent 像办公室里的一个员工
- 能看到：
  - 当前是否活跃
  - 当前在跑什么命令 / tool 摘要
  - 当前模型
  - 上下文 / token / cost 等占用情况
- 如果存在 subagent，也希望被可视化
- 如果一个 agent 有多个 channel，希望 channel 能单独被监控，而不是糊成一坨

## 已确认的 Hermes 数据基础

Hermes 当前数据结构已经具备做这件事的基础，不需要硬猜：

### 1. runtime / profile 维度

Hermes 支持多个 profile / `HERMES_HOME`，每个 profile 都有独立：

- `state.db`
- `gateway_state.json`
- `sessions/sessions.json`
- `channel_directory.json`
- logs / cron / auth / config

这意味着“一个机器上多个独立 Hermes runtime”天然可以拆开监控。

### 2. channel / session 维度

`gateway/session.py` 里的 `SessionSource` 已包含：

- `platform`
- `chat_id`
- `chat_name`
- `chat_type`
- `user_id`
- `user_name`
- `thread_id`
- `chat_topic`

这意味着同一个 Hermes runtime 下，不同 channel / thread 可以拆成独立监控对象。

### 3. 状态与用量维度

`hermes_state.py` 里的 `sessions` / `messages` 已有：

- `model`
- `message_count`
- `tool_call_count`
- `input_tokens`
- `output_tokens`
- `cache_read_tokens`
- `cache_write_tokens`
- `reasoning_tokens`
- `estimated_cost_usd`
- `actual_cost_usd`

`gateway_state.json` 则可提供 gateway alive / stopped 等运行态。

## 参考项目判断

### 1. `tmdgusya/argus`

优点：

- 方向对，明确想做 Hermes 多 profile 监控
- 文档梳理了 Hermes 数据源与 schema

问题：

- 目前只是设计 / 文档仓库
- 没有真正实现
- 不能直接拿来用

结论：

- **更适合作为数据盘点参考，不适合作为现成方案**

### 2. `rolandal/pixel-agents-standalone`

优点：

- 已经是 standalone web app
- 视觉与交互非常贴近目标：像素办公室、员工、路径移动、状态气泡、subagent
- 前端已具备：
  - 办公室引擎
  - 角色状态动画
  - 工位 / 布局编辑器
  - 子 agent 可视化

问题：

- 数据接入层是 Claude Code transcript/JSONL 专用
- watcher / parser 假设不适配 Hermes

结论：

- **非常适合作为 UI / 动画壳子的参考甚至部分移植对象**
- **不适合作为 Hermes 数据层直接复用**

### 3. `workadventure/workadventure`

优点：

- 虚拟办公室 / 16-bit RPG 气质强
- 空间表达能力很强

问题：

- 太重
- 更像虚拟办公产品，不像监控产品
- 为监控改它，代价偏大

结论：

- **可作为地图/房间感参考，不建议作为主底座**

## 推荐架构

## 1. 总体结构

建议采用：

- **云上中心站（AWS / 阿里云 ECS）**
- **每台 Mac 本地 collector 主动上报**
- **前端像素办公室看板**

也就是：

```text
Home Mac A ─┐
Home Mac B ─┼─> Monitor Hub (cloud) ─> Web Dashboard
Home Mac C ─┘
```

### 为什么这样做

因为用户当前机器都在家里，且没有公网 IP。

因此不应走：

- 云端主动访问家里机器
- 暴露本地端口
- FRP / 端口映射 / 公网打洞作为主路径

应该走：

- **每台机器主动出站连接云端**

这是最稳、最标准的监控架构。

## 2. V1 通信模式

V1 建议先做：

- **HTTPS 快照上报**（每 3~5 秒）

而不是一开始就做完整实时事件流。

原因：

- 更容易落地
- 更适合先验证数据模型和 UI 表达
- 后续可平滑升级到 WebSocket 增量事件

## 3. 云端组件

### `monitor-hub`

职责：

- 接收各 collector 上报
- 维护实时状态
- 对前端提供：
  - REST 查询
  - WebSocket 推送

建议初期单机即可。

### `dashboard-web`

职责：

- 像素办公室前端
- 读取 hub 当前状态
- 将 machine / runtime / channel / subagent 映射为办公室场景

### 存储

V1 可选：

- SQLite：够用
- 或 Postgres：如果希望后续扩展历史与查询

V1 其实甚至可以“内存实时态 + 落盘快照”起步。

## 数据模型建议

建议把监控对象拆成 5 层：

### 1. Machine

一台物理机器。

字段示例：

- `machine_id`
- `machine_name`
- `last_seen_at`
- `online`
- `os`

### 2. Runtime

这台机器上的一个 Hermes 运行单元，可近似映射为一个 profile / `HERMES_HOME`。

字段示例：

- `runtime_id`
- `profile_name`
- `hermes_home`
- `gateway_state`
- `connected_platforms`
- `current_model`

### 3. Channel

同一 runtime 下的 channel / thread 维度。

字段示例：

- `platform`
- `chat_id`
- `chat_name`
- `chat_type`
- `thread_id`
- `chat_topic`
- `is_active`
- `last_message_at`

这是本方案的重点：

> 同一个 agent 下多个 channel 必须作为独立监控对象，而不是只看 runtime 总体状态。

### 4. Session

当前活跃会话 / 任务。

字段示例：

- `session_id`
- `model`
- `started_at`
- `message_count`
- `tool_call_count`
- `input_tokens`
- `output_tokens`
- `cache_tokens`
- `reasoning_tokens`
- `estimated_cost_usd`

### 5. Subagent

如果当前 session 里出现 delegate/subtask/subagent 行为，作为子对象展示。

字段示例：

- `subagent_id`
- `parent_runtime_id`
- `parent_session_id`
- `parent_tool_id`
- `current_activity`
- `status`

## UI 映射建议

### 1. 空间映射

推荐映射关系：

- **一层办公室 / 一个区域 = 一台机器**
- **办公室里的一个员工 = 一个 Hermes runtime/profile**
- **员工身边的小牌子 / 小终端 / 小工位簇 = channel**
- **员工头顶气泡 = 当前 tool / command 摘要**
- **员工旁边的小分身 = subagent**

这样既保留空间感，又不会因为 channel 太多导致人物爆炸。

### 2. 状态映射

建议先统一成这些状态：

- `offline`
- `idle`
- `active`
- `waiting`
- `blocked`
- `error`

动画建议：

- `idle`：坐着 / 闲逛
- `active`：敲字 / 在工位工作
- `reading`（可作为 active 子态）：看资料动作
- `waiting`：头顶省略号 / 站立发呆
- `blocked`：红色提示气泡

### 3. channel 表达原则

V1 不建议“一 channel 一个人物”。

原因：

- 画面会很乱
- channel 数量可能远大于 runtime 数量
- 用户主要想看的是“谁在忙、忙什么、在哪个 channel 忙”

因此更合理的是：

- **runtime 作为主人物**
- **channel 作为 runtime 的附属可视化对象**

例如：

- 工位边标签
- 小终端屏
- 小房间门牌
- 右侧详情面板

## 采集边界

## V1 采集内容

默认只采 **只读监控元数据**：

- 机器在线状态
- runtime/profile 状态
- gateway alive/stopped
- 当前 model
- 当前活跃 session 数
- 当前活跃 channel
- 当前 tool / command 摘要
- token / context / cost 占用
- subagent 数量与状态

## V1 不采内容

默认不上报：

- 完整 prompt
- 完整用户消息正文
- 完整 assistant 输出
- 原始文件内容
- 完整 shell 命令参数

建议只上传：

- `display_command`（显示摘要）

例如：

- `pytest tests/tools`
- `git push`
- `read README.md`
- `delegate_task: review backend`

而不是完整原始命令行。

## 鉴权与公网暴露策略

### 1. 看板访问

V1 不做登录系统。

采用：

- **长随机 URL**

例如：

```text
https://domain.example/board/<long-random-token>
```

这个方案适合作为 V1：

- 开发快
- 够用
- 不引入完整登录系统复杂度

但要明确：

- 这不是严格安全边界
- 真正安全仍然依赖“最小暴露数据 + collector 鉴权 + 不上传敏感正文”

### 2. Collector 上报

这个必须强鉴权。

建议：

- 每台机器一个 `collector_token`
- hub 侧绑定 `token -> machine_id`
- 支持吊销 / 轮换

### 3. 安全原则

V1 的安全边界应定义为：

- **访问侧弱鉴权**（长随机 URL）
- **采集侧强鉴权**（collector token）
- **数据侧最小暴露**（只读元数据，不传正文）

## 为什么不建议直接裸奔公网

即便只读，也不建议完全不设门槛。

原因：

- 当前命令摘要本身就可能泄露项目和工作流结构
- machine/profile/channel 名称也可能暴露敏感上下文
- 一旦被爬虫、缓存、收录，后补救成本很高

因此“长随机 URL”可接受，但“完全公开无门槛”不推荐。

## 技术路线拍板

### 推荐

- **前端壳子参考 `pixel-agents-standalone`**
- **后端数据层单独做 Hermes Monitor Hub**
- **每台 Mac 跑本地 collector 主动出站上报**
- **云上 AWS/阿里云单机先起步**

### 不推荐

- 直接拿 WorkAdventure 做主方案
- 直接让云端主动拉家里机器数据
- 直接复用 Claude transcript parser

## V1 范围

### 必做

- 多机器接入
- 多 runtime/profile 展示
- channel 独立监控
- 当前 model / status / tool 摘要
- subagent 基础可视化
- 像素办公室主视图
- 长随机 URL
- collector token 鉴权

### 暂不做

- 控制指令下发
- 远程重启
- 复杂登录权限系统
- 完整消息内容检索
- 告警系统
- 时间回放

## 当前判断

这个项目最有价值的地方，不是“再做一个普通 dashboard”，而是：

> 把多机、多 runtime、多 channel、多 subagent 的 Hermes 运行态，收敛成一个具有空间感和可读性的像素办公室观察界面。

这个方向是成立的。

技术上最关键的不是画面，而是 **collector → hub 的稳定数据模型**。

如果后续继续推进，下一步应该补：

1. 页面信息架构
2. 办公室布局草图
3. collector / hub API 草案
4. V1 的状态字段定义

## 设计升级：从 Hermes 专用看板升级为统一 Agent Runtime 观察台

后续讨论后，产品定位进一步收口：

> 这不应是一个只服务 Hermes 的专用看板，而应是一个面向多种 agent runtime 的统一运行态观察系统。

Hermes 是第一种 adaptor，不是系统本体。

### 为什么必须这么设计

原因很直接：

- 后续不只会接 Hermes
- 还可能接 OpenClaw / Claude Code / Codex / 其他 agent runtime
- 不同 agent 的原始数据结构不同，但“运行态语义”高度相似

真正需要统一观察的是这些抽象对象：

- 哪台机器在线
- 哪个 runtime 在忙
- 正在哪个 workspace / channel 忙
- 当前 activity 是什么
- **每个 agent 当前实际使用的模型是什么**
- 当前模型和资源占用如何
- 有没有 subagent
- 有没有卡住 / 等待 / 授权 / 报错

因此系统应围绕“统一运行态协议”设计，而不是围绕 Hermes schema 设计。

## 最终产品分层

### 1. Protocol 层

定义统一对象模型，不带 Hermes 私货。

这层只定义：

- `machine`
- `runtime`
- `workspace`
- `channel`
- `session`
- `task`
- `subagent`
- `activity`
- `resource_usage`
- `snapshot/event`

### 2. Adaptor 层

每种 agent runtime 各自实现 adaptor，把自己的原始状态翻译为统一协议：

- `hermes-adaptor`
- `openclaw-adaptor`
- `claude-code-adaptor`
- `codex-adaptor`

### 3. Collector 层

部署在每台机器上。

职责：

- 调用本地 adaptor
- 采集并归一化本机状态
- 将快照 / 事件上报到云端 hub

### 4. Hub + Board 层

部署在云端。

职责：

- 接收统一协议数据
- 维护实时态和历史态
- 给前端办公室看板提供查询和推送接口

一句话结构：

```text
local runtime -> adaptor -> collector -> cloud hub -> office board
```

## 最终视觉映射

### 1. 人物 = runtime 实体

人物不再绑定某一种产品，而是统一协议中的 `runtime`。

这意味着：

- Hermes runtime 是一个员工
- OpenClaw runtime 也是一个员工
- Claude Code runtime 也可以是一个员工

不同 runtime 类型只通过工牌颜色 / badge / 小图标区分，而不是用不同页面体系。

### 2. Channel = 可选子层

不是所有 runtime 都有 channel 概念。

因此 `channel` 必须是统一协议中的 **可选子层**，而不是强制所有 agent 都实现。

- Hermes 天然有 channel / thread / session source 维度
- 别的 agent 如果没有，就可以为空

视觉上：

- 人物本体 = runtime
- channel = 人物周围的小工位 / 门牌 / 状态牌
- 当前活跃 channel 高亮

### 3. Subagent = 小分身

subagent 应作为全局支持对象，而不是 Hermes 特性。

因为：

- OpenClaw 风格系统天然会有 subagent
- Claude Code / Codex / 未来 agent runtime 也可能有 delegate / worker / child agent 概念

视觉上：

- subagent 是从主人物旁边临时出现的小分身
- 带归属感
- 完成任务后消失

### 4. 机器 = 办公室分区

为了避免全局大平层失控，建议：

- 一台机器 = 一个办公室区域 / 楼层 / 房间
- 同一台机器上的多个 runtime 共享这个区域
- 机器离线时整个分区变暗

## 最终页面结构

### 页面 1：主看板

目标：一眼看全局。

内容：

- 按机器分区的像素办公室
- 每个 runtime 一个员工人物
- 每个人物显示：
  - 状态
  - 当前 activity 摘要
  - model
  - context 档位
- channel 作为附属对象
- subagent 作为小分身

### 页面 2：runtime 详情抽屉

点击人物打开。

内容：

- runtime 类型（Hermes / OpenClaw / Claude Code / Codex）
- machine
- profile / workspace
- 当前 model
- 当前状态
- 当前 activity
- token / context / cost
- 当前 channel 列表
- subagent 列表

### 页面 3：channel 详情

只对支持 channel 的 runtime 提供。

内容：

- channel 标识
- 最近活跃时间
- 当前关联 session / task
- 当前 activity 摘要
- 最近 subagent 活动

## 统一协议 v0：建议字段方向

### Runtime

建议至少包含：

- `runtime_id`
- `runtime_type`
- `display_name`
- `machine_id`
- `workspace_id`
- `status`
- `model`
- `model_provider`
- `model_display`
- `model_source`
- `current_activity`
- `context_usage`
- `token_usage`
- `cost_estimate`
- `last_seen_at`

其中模型字段建议明确区分：

- `model`：规范化后的模型标识（如 `anthropic/claude-sonnet-4`）
- `model_provider`：提供方（如 `anthropic` / `openai` / `zai`）
- `model_display`：给前端直接展示的短标签（如 `Claude Sonnet 4` / `GPT-5.4`）
- `model_source`：模型信息来源（如 `session` / `config` / `runtime_observed`）

原因：

> 这个看板里，“每个 agent 当前实际在用什么模型”是一级信息，不应该只作为附带字段模糊处理。

### Channel

建议至少包含：

- `channel_id`
- `runtime_id`
- `channel_type`
- `label`
- `is_active`
- `last_activity_at`
- `session_count`

### Subagent

建议至少包含：

- `subagent_id`
- `parent_runtime_id`
- `parent_task_id`
- `label`
- `status`
- `current_activity`
- `started_at`

### Activity

建议统一为：

- `kind`
  - `idle`
  - `reading`
  - `writing`
  - `tool_call`
  - `waiting_input`
  - `waiting_approval`
  - `blocked`
  - `error`
- `summary`
- `raw_type`（可选）

## 采集与安全边界的最终拍板

### 采集方式

- 每台机器本地 collector 主动出站到云端
- 不依赖家宽公网 IP
- 不让 hub 直接访问家里机器

### 访问方式

- V1 看板访问：长随机 URL
- V1 不做登录系统

### 机器鉴权

- collector -> hub 必须使用独立 token
- token 绑定 machine_id
- 支持轮换和吊销

### 数据暴露原则

默认只上传监控元数据，不上传：

- 完整对话正文
- prompt
- 原始文件内容
- 完整 shell 参数

## 当前最终判断

到这里，系统顶层定义已经明确：

> 这是一个基于统一运行态协议的像素办公室观察台。
> Hermes 是第一种 adaptor，不是产品边界。

因此后续推进顺序建议固定为：

1. 统一协议 v0
2. Hermes adaptor v0
3. collector / hub 数据链路
4. 像素办公室前端映射
5. 再引入第二种 agent adaptor 验证抽象是否稳定

## Agent Runtime Monitor Protocol v0（正式字段草案）

这一版先按 **snapshot-first** 设计。

也就是：

- V0 以“周期性状态快照”作为主数据面
- event 流先作为可选增强，不作为系统成立前提
- 前端主看板默认消费 snapshot

### 顶层对象：`monitor_snapshot`

| 字段 | 类型 | 必填 | 说明 |
|---|---|---:|---|
| `protocol_version` | string | 是 | 当前协议版本，V0 固定为 `armp.v0` |
| `snapshot_id` | string | 是 | 本次快照唯一 ID |
| `collector_id` | string | 是 | 采集器实例 ID |
| `collector_type` | string | 是 | 采集器类型，如 `hermes-collector` |
| `generated_at` | string(ISO8601) | 是 | 快照生成时间 |
| `machine` | object | 是 | 当前机器信息 |
| `runtimes` | object[] | 是 | 本机所有 runtime 列表 |
| `metrics` | object | 否 | 机器级聚合指标 |
| `warnings` | string[] | 否 | 本次采集发现的告警/退化信息 |

### 对象：`machine`

| 字段 | 类型 | 必填 | 说明 |
|---|---|---:|---|
| `machine_id` | string | 是 | 机器稳定标识 |
| `machine_name` | string | 是 | 展示名 |
| `hostname` | string | 否 | 本机 hostname |
| `os` | string | 否 | 如 `macOS` / `Linux` |
| `arch` | string | 否 | 如 `arm64` / `x86_64` |
| `online` | boolean | 是 | 该快照里固定为 `true`，由 hub 结合心跳判断离线 |
| `last_seen_at` | string(ISO8601) | 是 | 本机最近可见时间 |
| `network_zone` | string | 否 | 如 `home` / `office` / `cloud` |
| `labels` | string[] | 否 | 机器标签 |

### 对象：`runtime`

| 字段 | 类型 | 必填 | 说明 |
|---|---|---:|---|
| `runtime_id` | string | 是 | runtime 稳定标识 |
| `runtime_type` | string | 是 | 如 `hermes` / `openclaw` / `claude-code` |
| `display_name` | string | 是 | 前端展示名 |
| `machine_id` | string | 是 | 所属机器 |
| `workspace_id` | string | 否 | 所属 workspace |
| `status` | enum | 是 | `offline`/`idle`/`active`/`reading`/`waiting`/`blocked`/`error` |
| `activity` | object | 否 | 当前活动对象 |
| `model` | string | 否 | 规范化模型 ID |
| `model_provider` | string | 否 | 模型提供方 |
| `model_display` | string | 否 | 前端展示短名 |
| `model_source` | string | 否 | 模型来源，如 `session` / `config` / `runtime_observed` |
| `context_usage` | object | 否 | 上下文占用 |
| `token_usage` | object | 否 | token 指标 |
| `cost_estimate` | object | 否 | 成本估计 |
| `connected` | boolean | 是 | runtime 是否可连接/可工作 |
| `last_activity_at` | string(ISO8601) | 否 | 最近活动时间 |
| `uptime_seconds` | integer | 否 | 运行时长 |
| `workspace_label` | string | 否 | 展示用 workspace 名 |
| `channels` | object[] | 否 | 支持 channel 时填写 |
| `subagents` | object[] | 否 | 当前可见 subagent |
| `sessions` | object[] | 否 | 当前活跃 session 摘要 |
| `capabilities` | string[] | 否 | 如 `channels`, `subagents`, `cost_tracking` |
| `metadata` | object | 否 | runtime 私有扩展 |

### 对象：`activity`

| 字段 | 类型 | 必填 | 说明 |
|---|---|---:|---|
| `kind` | enum | 是 | `idle` / `reading` / `writing` / `tool_call` / `waiting_input` / `waiting_approval` / `blocked` / `error` |
| `summary` | string | 是 | 给 UI 直接展示的摘要 |
| `detail` | string | 否 | 更细说明，用于详情抽屉 |
| `tool_name` | string | 否 | 当前工具名 |
| `command_display` | string | 否 | 截断/脱敏后的命令摘要 |
| `channel_id` | string | 否 | 当前 activity 关联的 channel |
| `session_id` | string | 否 | 当前 activity 关联的 session |
| `started_at` | string(ISO8601) | 否 | 活动开始时间 |
| `updated_at` | string(ISO8601) | 否 | 最近更新时间 |
| `raw_type` | string | 否 | 底层系统原始事件类型 |

### 对象：`context_usage`

| 字段 | 类型 | 必填 | 说明 |
|---|---|---:|---|
| `percent` | number | 否 | 0-100 |
| `band` | enum | 否 | `low` / `medium` / `high` / `critical` |
| `window_tokens` | integer | 否 | 模型上下文窗口大小 |
| `used_tokens` | integer | 否 | 已使用上下文 token |
| `source` | string | 否 | 指标来源 |

### 对象：`token_usage`

| 字段 | 类型 | 必填 | 说明 |
|---|---|---:|---|
| `input_tokens` | integer | 否 | 输入 token |
| `output_tokens` | integer | 否 | 输出 token |
| `cache_read_tokens` | integer | 否 | cache read |
| `cache_write_tokens` | integer | 否 | cache write |
| `reasoning_tokens` | integer | 否 | reasoning token |
| `total_tokens` | integer | 否 | 聚合值 |
| `window_scope` | string | 否 | 如 `current_session` / `rolling_1h` |

### 对象：`cost_estimate`

| 字段 | 类型 | 必填 | 说明 |
|---|---|---:|---|
| `estimated_usd` | number | 否 | 估算成本 |
| `actual_usd` | number | 否 | 实际成本（若已知） |
| `billing_provider` | string | 否 | 计费提供方 |
| `status` | string | 否 | `estimated` / `actual` / `unknown` |
| `window_scope` | string | 否 | 指标窗口 |

### 对象：`channel`

> `channel` 是可选子层。只有 runtime 支持该概念时才上报。

| 字段 | 类型 | 必填 | 说明 |
|---|---|---:|---|
| `channel_id` | string | 是 | channel 稳定标识 |
| `runtime_id` | string | 是 | 所属 runtime |
| `channel_type` | string | 是 | 如 `dm` / `group` / `thread` / `channel` |
| `platform` | string | 否 | 如 `feishu` / `telegram` / `discord` |
| `label` | string | 是 | 展示名 |
| `topic` | string | 否 | 主题/描述 |
| `is_active` | boolean | 是 | 当前是否活跃 |
| `status` | enum | 否 | `idle` / `active` / `waiting` / `blocked` / `error` |
| `last_activity_at` | string(ISO8601) | 否 | 最近活动时间 |
| `session_count` | integer | 否 | 该 channel 下 session 数 |
| `current_session_id` | string | 否 | 当前活跃 session |
| `current_activity` | object | 否 | 当前 channel 活动 |
| `metadata` | object | 否 | 平台私有扩展 |

### 对象：`session`

| 字段 | 类型 | 必填 | 说明 |
|---|---|---:|---|
| `session_id` | string | 是 | session 唯一标识 |
| `runtime_id` | string | 是 | 所属 runtime |
| `channel_id` | string | 否 | 所属 channel |
| `status` | enum | 是 | `active` / `waiting` / `ended` / `error` |
| `model` | string | 否 | 当前 session 模型 |
| `title` | string | 否 | session 标题 |
| `started_at` | string(ISO8601) | 否 | 开始时间 |
| `ended_at` | string(ISO8601) | 否 | 结束时间 |
| `message_count` | integer | 否 | 消息数 |
| `tool_call_count` | integer | 否 | 工具调用数 |
| `activity` | object | 否 | 当前 session 活动 |

### 对象：`subagent`

| 字段 | 类型 | 必填 | 说明 |
|---|---|---:|---|
| `subagent_id` | string | 是 | subagent 稳定标识 |
| `parent_runtime_id` | string | 是 | 所属 runtime |
| `parent_session_id` | string | 否 | 所属 session |
| `parent_task_id` | string | 否 | 所属 task/tool |
| `label` | string | 是 | 展示名 |
| `status` | enum | 是 | `active` / `waiting` / `blocked` / `done` / `error` |
| `activity` | object | 否 | 当前活动 |
| `started_at` | string(ISO8601) | 否 | 开始时间 |
| `updated_at` | string(ISO8601) | 否 | 最近更新时间 |
| `ephemeral` | boolean | 否 | 是否临时实体 |

### 对象：`metrics`

| 字段 | 类型 | 必填 | 说明 |
|---|---|---:|---|
| `runtime_count` | integer | 否 | runtime 总数 |
| `active_runtime_count` | integer | 否 | 活跃 runtime 数 |
| `active_channel_count` | integer | 否 | 活跃 channel 数 |
| `active_subagent_count` | integer | 否 | 活跃 subagent 数 |
| `estimated_cost_usd` | number | 否 | 本快照窗口内总成本 |
| `total_tokens` | integer | 否 | 本快照窗口内 token 总量 |

## 兼容性与约束

### 1. snapshot-first

V0 先确保“没有 event 流，也能工作”。

### 2. channel 是可选层

- Hermes 这类有 source/channel 概念的系统应上报
- 没有 channel 概念的 runtime 可以不填

### 3. model 是一级字段

后续 adaptor 必须尽力回答：

> 这个 agent 当前实际在用哪个模型？

如果拿不到，也要明确 `model_source=unknown`，而不是静默缺失。

### 4. metadata 允许私有扩展

V0 不追求一次抽象完所有差异。

统一字段之外，允许：

- `runtime.metadata`
- `channel.metadata`
- `session.metadata`

承载过渡期私货，但主看板只依赖公共字段。
