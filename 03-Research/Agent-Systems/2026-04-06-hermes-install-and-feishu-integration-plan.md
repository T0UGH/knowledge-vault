# Hermes 安装与飞书接入计划

日期：2026-04-06
状态：ready

## 目标

明天开始把 Hermes 真正接到这台机器上，并接入飞书，作为一个可长期驻留、可独立对话、可做 QA / backup / 第二视角的第三个 agent 候选。

这份文档不追求“把 Hermes 研究透”，而是回答更实际的问题：

> **要把 Hermes 在这台机器上装起来，并接入飞书，最稳的路径是什么？**

---

## 先说结论

Hermes **原生支持 Feishu / Lark**，而且不是边缘功能，是正式 gateway 平台之一。

从仓库可见的直接证据包括：

- `gateway/platforms/feishu.py`
- `website/docs/user-guide/messaging/feishu.md`
- `tests/gateway/test_feishu.py`
- `hermes_cli/gateway.py` 中的 Feishu/Lark setup 向导
- `pyproject.toml` 中的 `feishu = ["lark-oapi>=1.5.3,<2"]`

所以这件事不是“能不能 hack 出来”，而是：

> **Hermes 本身就支持飞书，关键在于你要不要接，以及接成什么边界。**

---

## 当前判断

### 能力层面

Hermes 的飞书接入能力已经足够做第一轮试验：

- 支持 Feishu / Lark bot
- 支持 direct message
- 支持 group message（带 @mention gating）
- 支持 websocket 模式
- 支持 webhook 模式
- 支持 `FEISHU_HOME_CHANNEL` 用于 cron / 通知输出
- 支持 allowlist、group policy、bot identity 配置

### 架构层面

如果要把 Hermes 接进来，**建议先把它当作独立系统接入飞书，而不是先强行融入 MT / OpenClaw 当前主链路。**

换句话说：

- **第一阶段目标：跑通 Hermes 自己的飞书 bot 能力**
- **不是**第一天就去做 OpenClaw ↔ Hermes 深度桥接

这是更稳的顺序。

---

## 最推荐的接入方式

### 推荐：WebSocket 模式

Hermes 的 Feishu 文档里明确写了：

- `FEISHU_CONNECTION_MODE=websocket`
- 推荐用于 laptop / workstation / private server
- 不需要公网 webhook URL

这对当前这台机器很关键，因为它意味着：

- 不用先配公网反代
- 不用先折腾 webhook 验签回调
- 只要本地进程在线，就能和 Feishu 建立出站连接

所以**第一阶段最稳方案是：WebSocket mode。**

### 暂时不推荐：Webhook 模式

Webhook 模式不是不能用，但它会额外引入：

- HTTP endpoint
- webhook host / port / path
- 飞书开发者后台订阅回调
- 可达性与验签问题

如果明天的目标只是“先把 Hermes 接上飞书跑起来”，这条路没必要先走。

---

## 明天的推荐执行顺序

## Phase 1：本机安装 Hermes

### 方案 A：用官方安装脚本

Hermes README 提供的 quick install：

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

安装后：

```bash
source ~/.zshrc
hermes
```

这是最省事的路径，适合先把 CLI / 本地环境立起来。

### 方案 B：手动 clone + setup（更可控）

如果你想把仓库保留在本机、便于后续改配置或看代码，我更推荐：

```bash
git clone https://github.com/NousResearch/hermes-agent.git ~/workspace/hermes-agent
cd ~/workspace/hermes-agent
./setup-hermes.sh
```

这个脚本会做几件事：

- 安装 / 检测 `uv`
- 建 Python 3.11 venv
- 安装依赖
- 创建 `.env`
- 把 `hermes` 命令链接到 `~/.local/bin/hermes`

### 我的建议

**明天建议走方案 B。**

理由：

- 你不是只想临时玩一下 CLI
- 你大概率还会看它的 gateway / config / docs
- clone 到本机更方便控制和排查

---

## Phase 2：确认 Hermes 本体可跑

安装完成后，先不要急着接飞书。先确认最小本体正常：

```bash
hermes
```

然后至少确认这几件事：

1. `hermes` 命令可用
2. 能进入 CLI
3. model/provider 基础配置可完成
4. `~/.hermes/` 目录被正常创建

这一阶段如果都没过，就不要往飞书接。

---

## Phase 3：补 Feishu 依赖

Hermes 的飞书适配依赖 `lark-oapi`。

在仓库里它对应：

```toml
feishu = ["lark-oapi>=1.5.3,<2"]
```

所以如果安装脚本没有自动覆盖到这一项，至少要确认飞书相关依赖在环境里可用。

文档里也明确写了：

- 如果缺 Feishu 依赖，会提示：
  - `pip install 'hermes-agent[feishu]'`

### 建议动作

在 Hermes 的 venv 环境里确认：

```bash
source ~/workspace/hermes-agent/venv/bin/activate
python -c "import lark_oapi; print('ok')"
```

如果失败，再补装：

```bash
uv pip install -e ".[feishu]"
```

如果你已经装了更大的集合，也可以直接：

```bash
uv pip install -e ".[all]"
```

### 我的建议

**明天不要赌安装脚本是否已经带好 Feishu 依赖。**

最稳做法是：
- 安装后显式检查一次 `lark_oapi`
- 缺就补 `.[feishu]`

---

## Phase 4：创建飞书应用

要接 Hermes 到飞书，必须先有一个 Feishu / Lark App。

按官方文档，关键步骤是：

1. 打开开发者后台：
   - Feishu: `https://open.feishu.cn/`
   - Lark: `https://open.larksuite.com/`
2. 创建新应用
3. 在 **Credentials & Basic Info** 里拿到：
   - `App ID`
   - `App Secret`
4. 打开 **Bot** capability

### 这一步需要准备的最小信息

第一阶段只需要这些：

- `FEISHU_APP_ID`
- `FEISHU_APP_SECRET`
- `FEISHU_DOMAIN=feishu`
- `FEISHU_CONNECTION_MODE=websocket`

---

## Phase 5：配置 Hermes 的 Feishu 环境变量

Hermes 文档给的最小配置是：

```bash
FEISHU_APP_ID=cli_xxx
FEISHU_APP_SECRET=secret_xxx
FEISHU_DOMAIN=feishu
FEISHU_CONNECTION_MODE=websocket
```

推荐额外配置：

```bash
FEISHU_ALLOWED_USERS=ou_xxx,ou_yyy
FEISHU_HOME_CHANNEL=oc_xxx
```

### 我建议第一阶段最小配置长这样

写到：

- `~/.hermes/.env`

建议值：

```bash
FEISHU_APP_ID=...
FEISHU_APP_SECRET=...
FEISHU_DOMAIN=feishu
FEISHU_CONNECTION_MODE=websocket
FEISHU_ALLOWED_USERS=<你的 open_id>
FEISHU_GROUP_POLICY=allowlist
```

### 为什么第一阶段就要加 allowlist

因为你明天接进飞书后，这个 bot 很容易变成一个真实可被触达的入口。

为了避免一上来就变成“谁都能戳”的状态，建议第一阶段就收紧：

- 只允许你自己
- group policy 先用 `allowlist`

这比先开放再收口更稳。

---

## Phase 6：启动 Hermes gateway

飞书接入最终要跑的是 Hermes gateway：

```bash
hermes gateway
```

或者如果它有 setup / service 化流程，也可以先走：

```bash
hermes gateway setup
```

Hermes 文档里建议的交互式路径是：

```bash
hermes gateway setup
```

然后选择：

- **Feishu / Lark**

### 我的建议

明天更稳的顺序是：

1. 先手工把 `.env` 写好
2. 再跑 `hermes gateway`
3. 如果想补齐其他平台，再回头用 `hermes gateway setup`

原因：

- 你现在目标是“跑通飞书”
- 不是“把 Hermes 整套平台向导全部玩一遍”

---

## Phase 7：验证飞书链路

最小验证闭环建议这样做：

### 验证 1：进程是否正常启动

启动 gateway 后，确认：

- 没有缺依赖报错
- 没有 App ID / Secret 缺失报错
- 没有连接模式错误
- gateway 保持在线而不是秒退

### 验证 2：飞书私聊 bot

从飞书直接给 Hermes bot 发一条消息，确认：

- 能收到消息
- 能正常回话

### 验证 3：allowlist 生效

如果你设了 `FEISHU_ALLOWED_USERS`，确认：

- 允许用户可正常交互
- 非 allowlist 用户不会被处理

### 验证 4：group 行为

如果你把 bot 拉进群，确认：

- 没 @ 时不乱回
- @bot 时才响应

### 验证 5：home channel（可选）

如果有 `FEISHU_HOME_CHANNEL`，再验证：

- 它是否能作为通知 / cron 的默认落点

---

## 推荐的明日执行清单

如果明天我来带你落地，我会按这 8 步走：

1. 确认本机放置目录（建议 `~/workspace/hermes-agent`）
2. clone Hermes 仓库
3. 跑 `./setup-hermes.sh`
4. 验证 `hermes` CLI 可用
5. 检查 `lark_oapi` 是否存在；缺则补 `.[feishu]`
6. 在飞书开发者后台创建 Hermes bot app，拿 `App ID` / `App Secret`
7. 写 `~/.hermes/.env`：先上 websocket + allowlist 最小配置
8. 启动 `hermes gateway`，做私聊验证

这一套跑通之后，再决定要不要继续做：

- home channel
- group policy 精调
- cron 投递
- 和 MT / Hex / OpenClaw 的协作边界设计

---

## 我对风险的判断

### 风险 1：会不会和当前 OpenClaw 飞书链路冲突

**有可能，但不是必然。**

关键不在“都用飞书”，而在于：

- 是否复用同一个 Feishu app
- 是否试图占同一类入口角色

### 建议

第一阶段建议：

- **Hermes 用单独的 Feishu app**
- 不复用当前 MT / OpenClaw 那个 app

这样最清楚，也最不容易互相踩。

### 风险 2：Hermes 会不会抢主脑位置

会，如果不设边界。

所以第一阶段应该明确：

- Hermes 先作为独立实验 agent
- 不接管 MT 主记忆
- 不抢当前主控链路
- 先验证它作为 QA / backup / 第二脑是否成立

### 风险 3：依赖可能偏重

Hermes 本身是个大系统，不是轻量 CLI。

所以不要把明天目标定成：
- 装完 Hermes
- 接好飞书
- 跑 cron
- 迁移 OpenClaw
- 做多 agent 编排

这会直接把事情搞重。

正确目标应该是：

> **明天先把 Hermes 本体 + Feishu 私聊链路跑通。**

---

## 明日最小成功标准

如果明天结束时满足下面 4 条，我就认为“接入 Hermes 的第一阶段成功”：

1. 本机 Hermes 安装完成
2. Hermes gateway 可稳定运行
3. 飞书私聊 bot 可收发消息
4. allowlist / group policy 至少已做最小安全收口

只要这 4 条成立，后面再谈：

- QA 角色
- 第三 agent 定位
- 和 MT / Hex 的边界
- cron / home channel / group flows

才是顺的。

---

## 一句话总结

Hermes 接飞书这件事本身并不难，真正需要克制的是目标范围。

> **明天最稳的打法不是“把 Hermes 全部接好”，而是“把 Hermes 作为独立飞书 bot 跑通，并守住边界”。**
