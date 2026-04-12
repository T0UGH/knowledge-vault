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
