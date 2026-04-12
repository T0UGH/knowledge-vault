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

**经 review 收缩后，V0 核心只保留这些对象：**

- `collector`
- `machine`
- `runtime`
- `activity`
- `snapshot`

**可选子层：**

- `channel`
- `subagent`

**降级为非核心/后续对象：**

- `workspace`：先作为 runtime 属性或展示语义，不单独建模
- `session`：先作为 Hermes-first 的辅助明细层，不作为跨 runtime 核心对象
- `task`：不进入 V0 核心
- `resource_usage`：不保留泛对象，继续拆到 `context_usage` / `token_usage` / `cost_estimate`

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

V1 仍然不做完整用户系统，但**不再把长随机 URL 作为唯一读侧控制**。

最小可接受门槛改为：

- 长随机 URL
- 再叠加一层轻访问控制（任选其一）：
  - reverse-proxy basic auth
  - signed URL / expiring token
  - VPN / IP allowlist

### 机器鉴权

- collector -> hub 必须使用独立 token
- token 绑定 machine_id
- 支持轮换和吊销

### 数据出站 allowlist（V0 拍板）

**允许离开机器的字段：**

- machine / runtime / channel / subagent 的标识与状态字段
- model 的规范化标识与展示短名
- token / context / cost 的聚合数值
- 脱敏后的 `activity.summary`
- 脱敏后的 `command_display`

**默认禁止离开机器的字段：**

- 完整对话正文
- prompt
- 原始文件内容
- 绝对路径
- 完整 shell 参数
- repo 私有代号 / 客户名 / 敏感工单号
- 原始日志文本整段上传

### 命令与摘要脱敏原则

- `command_display` 只保留语义摘要
- 默认裁掉绝对路径、长参数、token、URL query、私有代号
- 若无法安全脱敏，宁可降级为泛化摘要，如 `Running shell command`

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
| `collector_seq` | integer | 是 | collector 侧单调递增序号，用于排序与去重 |
| `generated_at` | string(ISO8601) | 是 | 快照生成时间 |
| `machine` | object | 是 | 当前机器信息 |
| `runtimes` | object[] | 是 | 本机所有 runtime 列表 |
| `metrics` | object | 否 | 机器级聚合指标 |
| `warnings` | string[] | 否 | 本次采集发现的告警/退化信息 |

#### Snapshot 语义（V0 拍板）

- `collector_id + snapshot_id` = 幂等键
- `collector_seq` 必须单调递增
- 每次 snapshot 都被视为 **该 collector 的完整当前视图**
- 新 snapshot 到达后，hub 应以 `collector_seq` 较大的版本覆盖较小版本
- 若新 snapshot 中缺少旧版本存在的 child 对象（如 channel / subagent），默认进入 `stale -> TTL 删除` 流程，而不是永久保留

### 对象：`machine`

| 字段 | 类型 | 必填 | 说明 |
|---|---|---:|---|
| `machine_id` | string | 是 | 机器稳定标识 |
| `machine_name` | string | 是 | 展示名 |
| `hostname` | string | 否 | 本机 hostname |
| `os` | string | 否 | 如 `macOS` / `Linux` |
| `arch` | string | 否 | 如 `arm64` / `x86_64` |
| `last_seen_at` | string(ISO8601) | 是 | 本机最近可见时间 |
| `network_zone` | string | 否 | 如 `home` / `office` / `cloud` |
| `labels` | string[] | 否 | 机器标签 |

> `machine.online` 不再由 collector 直接上报。
> 机器 reachability 一律由 hub 基于最近成功 snapshot / heartbeat 推导。

### 对象：`runtime`

| 字段 | 类型 | 必填 | 说明 |
|---|---|---:|---|
| `runtime_id` | string | 是 | runtime 稳定标识 |
| `runtime_type` | string | 是 | 如 `hermes` / `openclaw` / `claude-code` |
| `display_name` | string | 是 | 前端展示名 |
| `machine_id` | string | 是 | 所属机器 |
| `workspace_label` | string | 否 | 展示用 workspace / profile 名 |
| `reachability_state` | enum | 是 | `online` / `stale` / `offline`，由 hub 推导 |
| `work_state` | enum | 是 | `idle` / `active` / `waiting` / `blocked` / `error` |
| `activity` | object | 否 | 当前活动对象 |
| `model` | string | 否 | 规范化模型 ID |
| `model_provider` | string | 否 | 模型提供方 |
| `model_display` | string | 否 | 前端展示短名 |
| `model_source` | string | 否 | 模型来源，如 `session` / `config` / `runtime_observed` / `unknown` |
| `context_usage` | object | 否 | 上下文占用 |
| `token_usage` | object | 否 | token 指标 |
| `cost_estimate` | object | 否 | 成本估计 |
| `last_activity_at` | string(ISO8601) | 否 | 最近活动时间 |
| `uptime_seconds` | integer | 否 | 运行时长 |
| `channels` | object[] | 否 | 支持 channel 时填写 |
| `subagents` | object[] | 否 | 当前可见 subagent |
| `session_summaries` | object[] | 否 | 当前/最近 session 摘要，非核心对象 |
| `capabilities` | string[] | 否 | 如 `channels`, `subagents`, `cost_tracking` |
| `metadata` | object | 否 | runtime 私有扩展 |

> `runtime.status` 与 `connected` 已拆除，避免混合 reachability、健康度和活动态。
> 主看板优先使用：`reachability_state + work_state + activity.kind` 三层语义。

### 对象：`activity`

| 字段 | 类型 | 必填 | 说明 |
|---|---|---:|---|
| `kind` | enum | 是 | `reading` / `writing` / `tool_call` / `waiting_input` / `waiting_approval` / `blocked` / `error` |
| `summary` | string | 是 | 给 UI 直接展示的摘要 |
| `detail` | string | 否 | 更细说明，用于详情抽屉 |
| `tool_name` | string | 否 | 当前工具名 |
| `command_display` | string | 否 | 截断/脱敏后的命令摘要 |
| `channel_id` | string | 否 | 当前 activity 关联的 channel |
| `session_id` | string | 否 | 当前 activity 关联的 session |
| `started_at` | string(ISO8601) | 否 | 活动开始时间 |
| `updated_at` | string(ISO8601) | 否 | 最近更新时间 |
| `raw_type` | string | 否 | 底层系统原始事件类型 |
| `source` | string | 否 | 推断来源，如 `session_tool`, `gateway_log`, `config`, `unknown` |
| `confidence` | enum | 否 | `high` / `medium` / `low` |

> `idle` / `active` / `waiting` 等语义不再放在 `activity.kind`，而由 `work_state` 承担。

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

## Hermes Adaptor v0（统一协议映射草案）

这一节回答的问题是：

> 现有 Hermes 数据结构，如何干净映射到 Agent Runtime Monitor Protocol v0？

结论先说：

- **Hermes 很适合作为第一号 adaptor**
- 因为它天然已经有：
  - runtime/profile 边界
  - channel/source 边界
  - session 持久化
  - token / cost / tool usage 数据
  - gateway 运行态
- 但也有几个缺口：
  - “当前正在跑什么命令”不一定总能从结构化字段直接拿到
  - subagent 目前更多是推断层，不是完整的一等状态表
  - “当前实际模型”有时要从 session 和 config 联合判断

### Hermes adaptor 的输入源

建议 V0 只读这些文件/数据源：

| Hermes 数据源 | 用途 |
|---|---|
| `state.db` | session / message / token / tool / model / cost 主来源 |
| `gateway_state.json` | gateway alive / stopped / platform 连接态 |
| `sessions/sessions.json` | channel/source/session 上下文索引 |
| `channel_directory.json` | 已知 channel 列表与展示标签 |
| `config.yaml` | 默认模型、provider、压缩/内存等运行配置 |
| `logs/gateway.log` | 补充最近 activity 和响应时序（可选） |
| `logs/errors.log` | 错误态和 blocked/error 归因（可选） |

V0 不建议一开始就深挖所有 log，只把 log 作为补充源。

### 映射单位

在 Hermes adaptor 里，我建议这样定义映射单位：

- **machine** = 当前这台运行 collector 的机器
- **runtime** = 一个 `HERMES_HOME` / profile
- **channel** = `SessionSource(platform, chat_id, thread_id)` 维度
- **session** = `state.db.sessions.id`
- **subagent** = `delegate_task` / 子任务观察到的临时实体

### 1. Hermes -> `machine`

来源：collector 本机环境。

| 协议字段 | Hermes来源 / 生成方式 |
|---|---|
| `machine.machine_id` | collector 配置里的稳定 ID，不直接依赖 hostname |
| `machine.machine_name` | collector 配置或本机 hostname |
| `machine.hostname` | 系统 hostname |
| `machine.os` | 本机 OS |
| `machine.arch` | 本机 CPU 架构 |
| `machine.last_seen_at` | 快照生成时间 |
| `machine.network_zone` | collector 配置，如 `home` |

### 2. Hermes -> `runtime`

runtime 直接对应一个 profile / `HERMES_HOME`。

如果一台机器只跑一个默认 profile，就一个 runtime。
如果一台机器上有多个 profiles，就每个 profile 各报一个 runtime。

| 协议字段 | Hermes来源 / 生成方式 |
|---|---|
| `runtime.runtime_id` | 固定为 `machine_id + ":" + profile_name`；若无 profile_name，则退化为 `machine_id + ":" + HERMES_HOME hash` |
| `runtime.runtime_type` | 固定为 `hermes` |
| `runtime.display_name` | profile 名，如 `default` / `backend` / `frontend` |
| `runtime.machine_id` | 对应 machine |
| `runtime.workspace_id` | V0 可用 profile 名或 workspace 配置生成 |
| `runtime.connected` | `gateway_state.json` + 本地文件可读性联合判断 |
| `runtime.status` | 由 gateway/session/activity 综合推断 |
| `runtime.last_activity_at` | 最近活跃 session / message / tool 时间 |
| `runtime.uptime_seconds` | 有 PID/start_time 时可推断，否则为空 |
| `runtime.capabilities` | 至少包含 `channels`, `cost_tracking`; 若探测到 delegation，则含 `subagents` |

### 3. Hermes -> `model*`

模型信息必须作为一级字段认真映射。

优先级建议：

1. **当前活跃 session 的 `sessions.model`**
2. `config.yaml` 的默认模型配置
3. 无法确认时填 `unknown`

| 协议字段 | Hermes来源 / 生成方式 |
|---|---|
| `runtime.model` | 当前活跃 session 的 `sessions.model`，否则 config 默认模型 |
| `runtime.model_provider` | 从 model / billing_provider / config.provider 归一化得到 |
| `runtime.model_display` | 对 `runtime.model` 做展示层短名映射 |
| `runtime.model_source` | `session` / `config` / `unknown` |

判断原则：

> 看板里展示的模型，应尽量回答“现在这个 agent 真正在用什么模型”，不是只展示默认配置。

### 4. Hermes -> `activity`

这是 Hermes adaptor 最关键的推断层。

V0 建议 activity 优先从以下信号组合得出：

1. 最近活跃 session 的最新 assistant/tool message
2. 最近活跃 tool call
3. gateway log 最近事件
4. 无结构化活动时退化为 idle / waiting

建议映射规则：

| Hermes信号 | 协议 `activity.kind` | 说明 |
|---|---|---|
| 最近有读类 tool（read/search/browser 等） | `reading` | 可直接映射阅读态 |
| 最近有 terminal / patch / write / code 类 tool | `writing` / `tool_call` | 视摘要内容决定 |
| 正在等待 clarify / approval | `waiting_input` / `waiting_approval` | 从交互型工具或等待态推断 |
| gateway 活着但近期无活动 | `idle` | 默认闲置 |
| 日志或状态里出现异常 | `error` / `blocked` | 有明确错误才升格 |

`activity.summary` 建议优先用：

- 当前 tool 名 + 摘要
- 最近命令的展示版
- 当前 channel/任务上下文的短语义

例如：

- `Reading README.md`
- `Running pytest tests/tools`
- `Waiting for clarify`
- `Reviewing channel: #backend-bugs`

### 5. Hermes -> `context_usage`

Hermes 没有一个天然“上下文占用百分比”单字段，因此 V0 需要估算。

建议：

- 先基于当前活跃 session 的 token 使用量估算
- 再结合模型上下文窗口大小，算出 `percent`

来源：

- `sessions.input_tokens`
- `sessions.output_tokens`
- `sessions.cache_read_tokens`
- `sessions.cache_write_tokens`
- 模型上下文长度（可来自配置或模型元数据映射）

如果拿不到准确窗口：

- `percent` 可为空
- 但至少给出 `band`

### 6. Hermes -> `token_usage`

这块 Hermes 很强，基本可直接映射。

| 协议字段 | Hermes来源 |
|---|---|
| `input_tokens` | `sessions.input_tokens` |
| `output_tokens` | `sessions.output_tokens` |
| `cache_read_tokens` | `sessions.cache_read_tokens` |
| `cache_write_tokens` | `sessions.cache_write_tokens` |
| `reasoning_tokens` | `sessions.reasoning_tokens` |
| `total_tokens` | 各项求和 |
| `window_scope` | V0 推荐 `current_session` |

### 7. Hermes -> `cost_estimate`

也可较直接映射。

| 协议字段 | Hermes来源 |
|---|---|
| `estimated_usd` | `sessions.estimated_cost_usd` |
| `actual_usd` | `sessions.actual_cost_usd` |
| `billing_provider` | `sessions.billing_provider` |
| `status` | 根据 `cost_status` / 是否有 actual 推断 |
| `window_scope` | `current_session` |

### 8. Hermes -> `channel`

Hermes 的 channel 粒度是成立的，来源主要是 `SessionSource` / `sessions.json` / `channel_directory.json`。

建议 channel 唯一键：

```text
runtime_id + ":" + platform + ":" + chat_id + ":" + thread_id(optional)
```

| 协议字段 | Hermes来源 / 生成方式 |
|---|---|
| `channel.channel_id` | `platform:chat_id[:thread_id]` |
| `channel.runtime_id` | 当前 profile 对应 runtime |
| `channel.channel_type` | `chat_type` |
| `channel.platform` | `origin.platform` |
| `channel.label` | `chat_name` / `channel_directory` 展示名 |
| `channel.topic` | `chat_topic` |
| `channel.is_active` | 最近活动时间是否落入活跃窗口 |
| `channel.status` | 最近 session/activity 综合推断 |
| `channel.last_activity_at` | 最近关联 session/message 时间 |
| `channel.session_count` | 该 channel 下 session 数 |
| `channel.current_session_id` | 最近活跃 session |
| `channel.current_activity` | 该 channel 最近活跃 activity |

### 9. Hermes -> `session`

Hermes 的 session 基本直接来自 `state.db.sessions`。

| 协议字段 | Hermes来源 |
|---|---|
| `session.session_id` | `sessions.id` |
| `session.runtime_id` | 当前 runtime |
| `session.channel_id` | 从 `sessions.json`/origin 反查或 join 映射 |
| `session.status` | `ended_at` 是否为空 + 最近活动推断 |
| `session.model` | `sessions.model` |
| `session.title` | `sessions.title` |
| `session.started_at` | `sessions.started_at` |
| `session.ended_at` | `sessions.ended_at` |
| `session.message_count` | `sessions.message_count` |
| `session.tool_call_count` | `sessions.tool_call_count` |
| `session.activity` | 从该 session 最近 message/tool 提炼 |

### 10. Hermes -> `subagent`

这是 Hermes adaptor 里最需要诚实处理的一块：

- Hermes 不是天然有一张“subagents”状态表
- V0 只能做 **观察性映射**，不能假装它已经是一等对象

建议 V0 先从这些信号推断：

- `delegate_task` 调用
- 父 session 的 delegation 观察
- 子 session 与 parent_session_id / child_session_id 关系
- 明确的 task / child agent 输出痕迹

V0 设计判断：

- **能观察到就上报临时 subagent**
- **观察不到就不要伪造**

| 协议字段 | Hermes来源 / 生成方式 |
|---|---|
| `subagent.subagent_id` | 优先 `child_session_id`；若不存在，则固定为 `parent_runtime_id + ":sub:" + parent_task_id + ":" + child_index` |
| `subagent.parent_runtime_id` | 当前 runtime |
| `subagent.parent_session_id` | 父 session |
| `subagent.parent_task_id` | `delegate_task` 对应 tool/task ID |
| `subagent.label` | 任务摘要 |
| `subagent.status` | `active` / `waiting` / `done` / `error` |
| `subagent.activity` | 最近 delegation 结果或子任务活动摘要 |
| `subagent.started_at` | delegation 开始时间 |
| `subagent.updated_at` | 最近观察时间 |
| `subagent.ephemeral` | V0 默认 `true` |

### 11. Hermes -> `runtime.status`

建议 runtime 状态由多信号综合推断：

| 条件 | `runtime.status` |
|---|---|
| collector 超时未上报（hub 层判断） | `offline` |
| gateway 有明显错误 / 日志异常 | `error` / `blocked` |
| 最近有读类 activity | `reading` |
| 最近有执行类 activity | `active` |
| 最近处于 clarify / approval 等等待态 | `waiting` |
| 在线但近期无活动 | `idle` |

关键点：

> `runtime.status` 是面向看板的稳定状态，不要求完全等于 Hermes 内部瞬时细节。

### Hermes adaptor v0 的边界

V0 我建议明确只承诺这些能力：

#### 承诺做到

- profile -> runtime 的稳定映射
- SessionSource -> channel 的稳定映射
- 当前模型尽量准确上报
- token / cost / gateway 状态稳定上报
- activity 能给出可读摘要
- subagent 能做基础观察型可视化

#### 暂不承诺

- 所有命令都能拿到完整结构化原文
- subagent 生命周期百分百精确
- 所有 runtime 状态都做到毫秒级实时
- 复杂事件流重放

### 我对 Hermes adaptor 的最终判断

Hermes 足够适合作为第一号 adaptor，原因是：

- 基础数据足够全
- channel 维度天然存在
- token / cost / model 都有落点
- 适合先把统一协议跑通

但也要接受一个现实：

> Hermes adaptor v0 更像“高质量运行态归一化器”，不是“无损镜像层”。

这没问题。V0 目标本来就不是一比一还原 Hermes 内部状态，而是给统一观察台喂一份 **稳定、可读、足够准** 的运行态视图。

## 办公室 UI 信息架构 v0

这一节解决的问题是：

> 统一协议和 Hermes adaptor 已经有了，前端到底该如何组织信息，才能既好看又真的可用？

我的拍板是：

- 它不是 Grafana，也不是游戏
- 它是一个 **带空间隐喻的只读运行态观察台**
- 主画面负责“扫全局”
- 右侧抽屉负责“看细节”
- channel 必须独立可见，但不能把主画面挤炸

### 设计原则

#### 1. 人物 = runtime，不是 session

一个角色代表一个 runtime / profile / agent worker，而不是一次 session。

原因：

- session 太短，会导致画面抖动
- runtime 才是用户真正持续观察的对象
- session 更适合进入详情面板，而不适合占主舞台

#### 2. channel 独立可见，但不是主角色

channel 必须单独监控，但不能和 runtime 抢层级。

因此：

- runtime = 主人物
- channel = 主人物周围的附属对象
  - 小工位
  - 小终端屏
  - 门牌
  - 任务卡

#### 3. subagent 是临时分身

subagent 在视觉上应表现为：

- 从主人物旁边冒出来
- 小一号
- 带归属感
- 完成后消失

它不是永久工位员工，而是 runtime 当前拆出来的临时执行单元。

#### 4. 机器必须有天然分区

一台机器 = 一个办公室区域 / 楼层 / 房间。

原因：

- 多机混在一个开放大平层会失控
- 机器边界是用户要看的一级信息
- 离线/在线状态最好通过整个区域表达，而不是只靠小字

### 页面结构

V0 建议只做 3 个主要视图。

### 页面 1：主看板（默认页）

目标：

- 一眼看出所有机器和所有 runtime 的整体运行态

结构：

- 页面主体是一个按机器分区的像素办公室
- 每个机器分区里有若干人物（runtime）
- 人物附近挂着 channel 附属对象
- 若有 subagent，则在人物附近生成小分身
- 页面右上角有全局状态条
- 页面左上角有过滤与搜索入口

#### 主看板必须回答的 6 个问题

1. 哪台机器在线？
2. 哪个 runtime 正在忙？
3. 它现在在干什么？
4. 当前在用什么模型？
5. 它正在忙哪个 channel？
6. 有没有 subagent / blocked / waiting？

### 页面 2：runtime 详情抽屉

触发：点击人物。

目标：

- 看清一个 runtime 的完整运行态，不离开主看板上下文

位置：

- 右侧滑出抽屉

抽屉内容建议分 5 个区块：

1. 基本身份
   - runtime 名
   - runtime_type
   - machine
   - workspace/profile

2. 当前状态
   - status
   - 当前 activity
   - 当前 model
   - context / tokens / cost

3. channels
   - 当前 active channel
   - 全部 channel 列表
   - 每个 channel 的最近活动时间

4. sessions
   - 当前 active session
   - 最近若干 session 摘要

5. subagents
   - 当前 subagent 数量
   - 每个 subagent 的 label / status / activity

### 页面 3：channel 详情视图

触发：点击 channel 小牌 / 小终端。

目标：

- 把“同一个 agent 下多个 channel”这层彻底看清

内容：

- channel 名称 / 类型 / platform
- 最近活动时间
- 当前 session
- 当前 activity
- 当前 model（若能下钻到 session）
- 最近 subagent 关联情况

对于没有 channel 概念的 runtime，这个视图可以不存在。

## 主看板信息密度设计

### 1. 机器分区层

每个机器分区显示：

- 机器名
- 在线/离线状态
- runtime 数
- 当前活跃 runtime 数
- 简单聚合指标（可选）

视觉建议：

- 在线机器：正常亮度
- 离线机器：整体变暗 + 灰化
- 异常机器：边框/角标高亮

### 2. 人物卡片层（runtime 主实体）

每个人物附近只显示最核心的 4 类信息，避免过载：

1. **状态**
   - idle / active / reading / waiting / blocked / error

2. **activity 摘要**
   - 例如 `Reading README.md`
   - 例如 `Running pytest`
   - 例如 `Waiting for clarify`

3. **模型短标签**
   - 例如 `Claude Sonnet 4`
   - 例如 `GPT-5.4`

4. **context 档位**
   - `L` / `M` / `H` / `!`
   - 或者小进度条

不建议把 token、cost、session 数全堆在人物头上。
这些应该放到抽屉里。

### 3. Channel 层

channel 的主画面表现建议采用“附属牌”形式：

每个 channel 至少显示：

- label（短名）
- active/idle 小状态
- 最近 activity 小图标

当前 active channel：

- 发光
- 有细连线连接到主人物
- 或显示成“被占用的工位”

#### 为什么不用“一 channel 一人物”

因为那会带来三个问题：

- 画面拥挤
- runtime 与 channel 的语义混淆
- runtime 多 channel 时难以表达主次关系

所以 V0 固定为：

> 人物是 runtime，channel 是附属对象。

### 4. Subagent 层

subagent 的画面规则建议固定：

- 比主人物小一号
- 颜色跟随父 runtime，但更浅
- 只显示短 label 或工具图标
- 最多在主画面显示 1~3 个
- 再多则合并成计数徽标，如 `+4`

否则一旦多 subagent 并发，主画面会失控。

## 右侧抽屉信息优先级

抽屉不是原始 JSON 展示器，仍然需要强排序。

建议优先级：

1. 当前状态与 activity
2. 当前模型
3. 当前 active channel
4. 当前 tokens / context / cost
5. 当前 subagents
6. 最近 sessions
7. 原始 metadata（折叠）

也就是说：

> 先回答“现在发生了什么”，再展示背景和统计。

## 全局导航与过滤

V0 我建议至少支持这些过滤器：

- 按 machine 过滤
- 按 runtime_type 过滤
- 按 status 过滤
- 只看有 subagent 的 runtime
- 只看 active / blocked / waiting

搜索建议支持：

- runtime 名
- machine 名
- channel label
- model_display

## 状态视觉规范

建议固定一套状态语义，别让颜色/动作乱飞。

| 状态 | 含义 | 视觉建议 |
|---|---|---|
| `idle` | 在线但近期无活动 | 坐着/轻微闲逛 |
| `active` | 正在执行 | 敲字/移动/工作动画 |
| `reading` | 读/检索/分析中 | 看书/面向屏幕阅读姿态 |
| `waiting` | 等待输入/授权/下游结果 | 省略号气泡/暂停态 |
| `blocked` | 卡住但未必错误 | 黄色告警牌 |
| `error` | 明确错误 | 红色异常牌 |
| `offline` | collector/hub 视角不可见 | 整体灰化 |

## 主画面布局建议

我建议不用“大开放办公室”，而是 **分区办公室**。

### 推荐布局

- 一行或两行机器分区
- 每个机器分区里：
  - 2~6 个 runtime 工位
  - 若干 channel 附属位
  - 一个小聚合信息区

### 不推荐布局

- 所有机器和所有 runtime 混在同一个大房间
- 让 channel 也占独立人物位
- 让 session 进入主画面成为一级实体

## V0 的 UI 成功标准

如果这个 UI 设计是对的，用户打开后应该能在几秒内回答：

- 哪台机器挂了
- 哪个 runtime 在忙
- 哪个 runtime 在等输入
- 哪个 runtime 带着 subagent
- 当前哪个 channel 最活跃
- 每个 runtime 在用什么模型

如果这些问题还需要频繁点进深层详情，说明主看板信息架构还不够好。

## 我对 UI v0 的最终判断

V0 应该坚持这条边界：

> 主看板负责“扫一眼就知道怎么回事”，详情抽屉负责“点进去看清楚为什么”。

因此最重要的不是做更多页面，而是把主看板上的：

- 机器分区
- runtime 人物
- channel 附属对象
- subagent 分身
- 模型 / activity / status 三元组

这几个核心视觉元素先稳定下来。

## Collector / Hub API 草案 v0

这一节回答的问题是：

> collector 怎么把状态发到云端 hub？
> hub 又怎么把当前状态给办公室看板？

V0 我建议采用：

- **collector -> hub：HTTP snapshot 上报为主**
- **hub -> board：REST 拉取 + WebSocket 推送结合**

也就是：

- 写入路径简单、稳定
- 读取路径兼顾初始化与实时更新

## 一、总体交互模型

### 1. Collector 上报路径

collector 周期性生成 `monitor_snapshot`，然后发给 hub：

```text
POST /api/v0/ingest/snapshots
```

V0 以 snapshot-first 为主，不要求 collector 先实现复杂 event 流。

### 2. Board 读取路径

前端办公室页面打开时：

1. 先走 REST 拉当前全量状态
2. 再连 WebSocket 收增量刷新

这样能避免：

- 页面首屏必须等事件流回放
- WebSocket 断线后状态全丢

## 二、Collector -> Hub API

### 1. `POST /api/v0/ingest/snapshots`

用途：

- collector 上报一个完整快照

#### 请求头

| Header | 必填 | 说明 |
|---|---:|---|
| `Authorization: Bearer <collector_token>` | 是 | collector 身份验证 |
| `Content-Type: application/json` | 是 | JSON body |
| `X-Collector-Id` | 否 | collector 实例 ID，便于日志追踪 |

#### 请求体

- body 即 `monitor_snapshot`

#### 约束

- 单次 body 应控制在合理大小内
- 若 snapshot 太大，collector 应先裁剪不必要 metadata
- V0 不做分片上传

#### 成功响应

```json
{
  "ok": true,
  "snapshot_id": "snap_20260412_xxx",
  "received_at": "2026-04-12T13:20:00Z",
  "next_poll_after_ms": 5000
}
```

#### 失败响应

```json
{
  "ok": false,
  "error": {
    "code": "INVALID_SNAPSHOT",
    "message": "runtime_id is missing"
  }
}
```

### 2. `POST /api/v0/ingest/heartbeat`

用途：

- 可选心跳接口
- 当 collector 暂时拿不到完整 snapshot 时，也能证明机器在线

V0 可以先不实现，或者把 heartbeat 融入 snapshot 的时间戳逻辑。

如果实现，建议 body：

```json
{
  "collector_id": "collector-macmini-a",
  "machine_id": "macmini-a",
  "sent_at": "2026-04-12T13:20:00Z"
}
```

### 3. `POST /api/v0/ingest/events`（deferred）

用途：

- 为未来 event 流预留
- **明确不属于 V0 最小实现面**

适合未来传：

- subagent start/done
- model switch
- runtime error
- channel active switch

V0 不要求实现该接口，主实现路径仍然只依赖 snapshot ingest。

## 三、Hub 认证与接入模型

### 1. Collector 身份模型

V0 建议：

- 每台机器一个 `collector_token`
- token 绑定：
  - `machine_id`
  - `collector_id`
  - `allowed_runtime_types`

#### 为什么不能只靠长随机 URL

因为：

- 长随机 URL 只适合浏览器访问
- collector 是写入端，必须强鉴权
- 否则任何人知道地址就能伪造状态

### 2. Hub 侧持久化对象

hub 接到 snapshot 后，应至少维护两层状态：

#### 实时态（current state）

供主看板使用：

- 当前在线机器
- 当前 runtime 状态
- 当前 active channel
- 当前 subagent

#### 历史态（optional / compact history）

供后续增强使用：

- 最近若干 snapshot
- runtime 状态切换记录
- 错误/blocked 轨迹

V0 可以先只做“当前态 + 最近 N 份 snapshot”。

## 四、Board 读取 API

### 1. `GET /api/v0/board/state`

用途：

- 前端首次加载时拉取整板当前状态

#### 响应建议

```json
{
  "ok": true,
  "generated_at": "2026-04-12T13:20:05Z",
  "machines": [...],
  "runtimes": [...],
  "channels": [...],
  "subagents": [...],
  "metrics": {...}
}
```

#### 设计判断

这里建议 hub 直接返回“已经平铺好的当前态”，不要让前端自己从原始 snapshot 拼装。

也就是说：

- ingest 层吃 snapshot
- query 层吐 board-friendly state

这是重要边界。

### 2. `GET /api/v0/board/machines`

用途：

- 机器列表
- 机器概览聚合

适合：

- 左侧机器导航
- 顶部机器筛选器

### 3. `GET /api/v0/board/runtimes`

用途：

- runtime 列表
- 支持筛选

建议支持 query 参数：

- `machine_id`
- `runtime_type`
- `status`
- `has_subagents`
- `model`

### 4. `GET /api/v0/board/runtimes/:runtime_id`

用途：

- runtime 详情抽屉数据

返回内容建议包括：

- runtime 基本信息
- activity
- model
- context/tokens/cost
- channels
- sessions
- subagents
- metadata

### 5. `GET /api/v0/board/channels/:channel_id`

用途：

- channel 详情视图

返回内容建议包括：

- channel 基本信息
- 当前 session
- 当前 activity
- 最近活动时间
- 关联 subagent 摘要

### 6. `GET /api/v0/board/filters`（deferred）

用途：

- 前端获取过滤项枚举

返回如：

- machine 列表
- runtime_type 列表
- model 列表
- status 枚举

但这一接口 **不属于 V0 最小实现**。
V0 可以先由前端基于 `/board/state` 本地派生过滤项，待主链路稳定后再单独补齐。

## 五、Board 实时推送 API

### `GET /api/v0/board/stream`（WebSocket）

用途：

- 给前端推送实时变化

V0 不建议把整个 snapshot 每次全量推给前端。
建议推送 **board_delta**。

#### 推送消息类型建议

| type | 用途 |
|---|---|
| `machine.updated` | 机器状态变更 |
| `runtime.updated` | runtime 状态或模型变更 |
| `channel.updated` | channel 状态变更 |
| `subagent.updated` | subagent 出现/消失/变更 |
| `board.metrics` | 全局聚合指标刷新 |
| `board.resync_required` | 前端应重新拉整板状态 |

#### 示例

```json
{
  "type": "runtime.updated",
  "sent_at": "2026-04-12T13:20:12Z",
  "payload": {
    "runtime_id": "macmini-a:backend",
    "status": "active",
    "model_display": "Claude Sonnet 4",
    "activity": {
      "kind": "tool_call",
      "summary": "Running pytest tests/tools"
    }
  }
}
```

#### 为什么需要 `board.resync_required`

因为 V0 的前端如果丢了一段 delta，不值得做复杂补偿。

更稳的做法是：

- 检测到状态不一致或版本跳变
- hub 直接告诉前端：重新调一次 `GET /board/state`

## 六、Hub 内部状态组织建议

虽然这是 API 草案，但内部结构会直接影响 API 是否干净。

我建议 hub 内部至少有这几张逻辑表/缓存：

### 1. `collectors`

记录：

- collector_id
- machine_id
- token_id
- last_seen_at
- last_snapshot_id
- auth status

### 2. `machine_current`

当前机器态。

### 3. `runtime_current`

当前 runtime 态。

### 4. `channel_current`

当前 channel 态。

### 5. `subagent_current`

当前 subagent 态。

### 6. `snapshot_history`

最近 N 份 snapshot。

V0 不一定真要做成数据库 6 张表，但逻辑上应该这么分。

## 七、版本、一致性与生命周期规则

### 1. Hub 当前态版本

hub 维护一个单调递增的 `state_version`。

规则：

- 每次成功应用一个较新的 snapshot，`state_version += 1`
- `GET /board/state` 必须返回当前 `state_version`
- WebSocket delta 也必须携带 `state_version`

### 2. Omission / stale / TTL 规则

当新 snapshot 中缺少旧 snapshot 里存在的对象时：

#### channel / subagent
- 不立即硬删除
- 先标记为 `stale`
- 超过 TTL 后再从 current state 移除

建议初始 TTL：
- `subagent_ttl_seconds = 30`
- `channel_ttl_seconds = 120`

#### session_summaries
- 允许直接按 recent-N 窗口重建
- 不要求 TTL 持有

### 3. Freshness / smoothing 规则

建议 V0 明确这些默认值：

- `machine_offline_after_seconds = 20`
- `machine_stale_after_seconds = 10`
- `runtime_active_window_seconds = 15`
- `runtime_waiting_window_seconds = 30`
- `subagent_min_visible_seconds = 8`

### 4. Detail API 边界

V0 默认：

- `session_summaries` = recent 10
- `subagents` 详情 = recent 10
- 若未来做 history，再单独分页

## 八、错误处理与退化策略

### 1. collector 上报失败

collector 收到非 2xx 时：

- 本地记录失败次数
- 指数退避重试
- 不要立刻丢弃最近一次成功状态

### 2. snapshot 部分字段缺失

hub 不应整包拒绝，除非关键键缺失。

#### 关键字段缺失才拒绝

例如：

- `protocol_version`
- `snapshot_id`
- `collector_id`
- `machine.machine_id`
- `runtime.runtime_id`

#### 非关键字段缺失则降级接收

例如：

- `model_display`
- `context_usage.percent`
- `subagent.activity`

### 3. 前端 WS 断线

断线后策略：

1. 自动重连
2. 重连成功后重拉一次 `GET /board/state`

而不是指望从断点续传 delta。

## 八、V0 API 成功标准

这套 API 如果设计对了，应该做到：

### 对 collector

- 上报逻辑简单
- 不需要理解前端布局
- 只要按协议吐 snapshot 即可

### 对 hub

- ingest 和 query 分层清楚
- 能稳定维护当前态
- 能给 board 返回可直接渲染的数据

### 对前端

- 首屏只要调一次 `GET /board/state`
- 后续主要吃 WS delta
- 需要时再调 runtime/channel 详情接口

## 我对 API v0 的最终判断

V0 不要贪，先保证这条链路稳定：

> collector 定时上报 snapshot → hub 维护当前态 → board 拉全量 + 吃增量推送。

这条链路一旦稳定，后面无论是：

- 接第二种 agent adaptor
- 加控制面
- 加历史回放
- 加告警

都会顺很多。
