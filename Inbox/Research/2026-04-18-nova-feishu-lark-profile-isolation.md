---
title: Nova 独立飞书 channel 与 lark-cli 凭据隔离方案
tags:
  - hermes
  - nova
  - feishu
  - lark-cli
  - isolation
---

# Nova 独立飞书 channel 与 lark-cli 凭据隔离方案

## 结论

可以给 `nova` 关联独立飞书 channel，但要分清两层隔离：

1. **Hermes gateway / Feishu bot 层**：
   - `nova` 可以配置独立 `FEISHU_HOME_CHANNEL`
   - 但如果要和 MT **同时在线独立收发**，不能共用同一个 `FEISHU_APP_ID / FEISHU_APP_SECRET`
   - 推荐给 `nova` 单独建一个飞书应用 / bot

2. **`lark-cli` 调用层**：
   - `lark-cli` 本身支持 **profile**
   - 最稳做法是给 MT / Nova 各建一个 `lark-cli profile`
   - 调用时固定带 `--profile`，不要共用默认身份

一句话判断：

> **channel 可以独立；真正要做到并发不串，bot 凭据和 lark-cli profile 都要独立。**

## 为什么 Feishu bot 凭据也要隔离

当前 Hermes 的 Feishu adapter 对同一个应用有 app-level lock。

已确认的实现特征：
- Feishu adapter 使用 `acquire_scoped_lock()` / `release_scoped_lock()`
- lock identity 绑定的是 `app_id`
- 这意味着两个 Hermes profile 不能同时用同一个飞书应用各自起一个独立 gateway

因此：
- **可以**：同一个 bot，单实例，发往不同 channel
- **不适合**：MT / Nova 想各自独立常驻、各自维护独立会话，却共用同一个 bot app

工程上更稳的方案是：
- MT 一套 Feishu app
- Nova 一套 Feishu app
- 各自配置自己的 home channel

## 为什么 `lark-cli` 的 appid / secret 也要隔离

如果 MT / Nova 共用一个 `lark-cli` 默认配置，会有三个问题：

1. **身份串位**
   - 命令是谁发的、实际走哪套 app 身份，不再清楚

2. **脚本容易误发**
   - 一旦某个脚本漏写 profile，就会落到当前 active profile

3. **迁移成本高**
   - 未来 Nova 独立迁机器时，很难只迁走属于 Nova 的飞书身份

所以这层也应该显式隔离。

## 已验证到的事实

### 1. `lark-cli` 支持 profile

本机 `lark-cli --help` / `lark-cli profile --help` 已确认支持：
- `profile add`
- `profile list`
- `profile remove`
- `profile rename`
- `profile use`
- 全局参数：`--profile <name>`

### 2. `lark-cli config init` 支持命名 profile

本机帮助信息已确认可用：

```bash
lark-cli config init --name <profile-name> --app-id <app-id> --app-secret-stdin --brand feishu
```

### 3. 当前本机 `lark-cli` 配置是单文件多 profile 模式

已看到：
- 配置文件路径：`/Users/wangguiping/.lark-cli/config.json`
- 当前 active profile 存在且可列出

这说明不需要额外魔改，直接用 profile 机制即可。

## 推荐方案

### A. Feishu bot / gateway 层

给 `nova` 单独建：
- `FEISHU_APP_ID`
- `FEISHU_APP_SECRET`
- `FEISHU_HOME_CHANNEL`
- 可选：`FEISHU_HOME_CHANNEL_NAME`

并放在 `nova` profile 自己的配置环境里，不和 MT 共用。

### B. `lark-cli` 层

建立两个 profile：
- `mt-feishu`
- `nova-feishu`

后续调用强制显式写：

```bash
lark-cli --profile mt-feishu ...
lark-cli --profile nova-feishu ...
```

### C. wrapper 层

为了避免人脑记忆，建议再包两层命令：

```bash
mt-lark   -> lark-cli --profile mt-feishu "$@"
nova-lark -> lark-cli --profile nova-feishu "$@"
```

这样做的价值：
- 减少手滑
- 降低脚本误发风险
- 让 skill / automation 更容易固化
- 未来 Nova 迁机器时迁移边界更清楚

## 不推荐的方案

### 方案 1：MT / Nova 共用一个 Feishu bot，只靠不同 channel 区分

不推荐原因：
- 只能做“目标 channel 区分”
- 做不到“独立常驻 + 双实例并发 + 清晰 ownership”
- 未来更容易串会话和责任边界

### 方案 2：MT / Nova 共用一个 `lark-cli` 默认 profile

不推荐原因：
- 默认身份不透明
- shell / script 一旦漏带参数就会串
- 不适合长期治理

## 最小落地步骤

### 第 1 步：先记录边界
- `nova` 有独立飞书 channel
- `nova` 有独立 `lark-cli profile`
- 后续凡是 Nova 触发飞书结构化操作，默认走 `nova-feishu`

### 第 2 步：创建 `lark-cli` profile

参考命令：

```bash
printf '%s' "$APP_SECRET" | lark-cli config init \
  --name nova-feishu \
  --app-id "$APP_ID" \
  --app-secret-stdin \
  --brand feishu
```

### 第 3 步：创建 wrapper

```bash
nova-lark() {
  lark-cli --profile nova-feishu "$@"
}
```

如果要做成可执行脚本，也可以固定放到本地 bin。

### 第 4 步：再接 Hermes nova profile

等 Nova 自己的飞书 app / channel 就位后，再把：
- `FEISHU_APP_ID`
- `FEISHU_APP_SECRET`
- `FEISHU_HOME_CHANNEL`

接进 `nova` 的 Hermes profile 环境。

## 验收标准

这件事只有同时满足下面几条才算真完成：

1. `nova` 有独立飞书 app / bot
2. `nova` 有独立 `FEISHU_HOME_CHANNEL`
3. `lark-cli profile list` 能看到 `nova-feishu`
4. `nova-lark ...` 发消息时，确认使用的是 `nova-feishu`
5. MT 和 Nova 的飞书结构化操作不会因为默认身份而串位

## 当前状态

当前只完成了方案判断和工具能力核实，还**没有**创建 `nova-feishu` profile，也**没有**接入新的飞书 app。

也就是说：
- **方案已收口**
- **执行尚未开始**

## 下一步建议

优先顺序建议是：

1. 先给 `nova` 单独申请一套飞书 app
2. 再创建 `lark-cli` 的 `nova-feishu` profile
3. 再做 `nova-lark` wrapper
4. 最后再把 Nova 的 Hermes gateway 接到独立 channel
