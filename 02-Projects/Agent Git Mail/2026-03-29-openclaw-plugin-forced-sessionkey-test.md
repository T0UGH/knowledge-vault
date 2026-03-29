---
title: OpenClaw Plugin Forced SessionKey 测试方案
date: 2026-03-29
tags:
  - agent
  - openclaw
  - plugin
  - verification
status: active
---

# OpenClaw Plugin Forced SessionKey 测试方案

## 目标

验证 `openclaw-agent-git-mail` plugin 能否在 **已知目标主会话** 的前提下，把“新信事件”送进正确的 OpenClaw session。

这轮测试**故意绕过自动 session binding**，先只验证：

> **新信检测 → inject / wake → 当前主会话可见提醒**

---

## 当前已知目标 sessionKey

本轮固定使用当前飞书直聊主会话：

```text
agent:main:feishu:direct:ou_1a5319752a5cf0f913864d68268200c4
```

说明：
- 这是当前与用户的 Feishu direct 主会话
- 这是本轮测试唯一目标会话
- 本轮不测试自动发现主会话的能力，只测试“已知主会话地址时能不能打进去”

---

## 测试原则

1. **不改测试目标**：只测 forced sessionKey 路径
2. **不引入多余变量**：不先做自动 binding 验证
3. **一定要留证据**：日志、命令输出、会话可见结果至少保留两类
4. **失败要可定位**：每一步都要知道失败点可能在哪

---

## 执行步骤

## Step 1：在 plugin 中临时加入 forced sessionKey 路径

目标：先不要走自动 session binding 查表，而是强制把新信提醒打到指定 sessionKey。

要求：
- 这条逻辑必须明确标注为 `verification-only`
- 正式实现不要默认长期保留为主路径

建议逻辑：
- 定义一个临时常量或临时配置项
- 若存在 `forcedSessionKey`，则新信事件直接投递到它
- 不查 `sessionBindings.get(name)`

目标值：

```text
agent:main:feishu:direct:ou_1a5319752a5cf0f913864d68268200c4
```

---

## Step 2：重新 build plugin

要求：
- `packages/openclaw-plugin` build 通过
- 不引入新的 TypeScript 错误

验证命令：

```bash
cd /Users/wangguiping/workspace/agent-git-mail
npm run build --workspace @t0u9h/openclaw-agent-git-mail
```

保留：
- build 输出

---

## Step 3：重新安装 / link plugin

要求：
- 使用本地 link 安装
- 确认 OpenClaw 能识别并加载 plugin

命令：

```bash
cd /Users/wangguiping/workspace/agent-git-mail
openclaw plugins install -l ./packages/openclaw-plugin
openclaw plugins info openclaw-agent-git-mail
```

通过标准：
- `Status: loaded`
- service 可见（如 `agent-git-mail-daemon`）

保留：
- `plugins info` 输出

---

## Step 4：准备测试 repo 与测试信件

目标：制造一封最小新信，让 watcher 能通过 `A inbox/*.md` 检测到。

要求：
- 信件符合当前 v0 协议
- 必须通过 git commit 进入历史

最小信件样例：

```md
---
from: hex
to: mt
subject: verification ping
created_at: 2026-03-29T21:50:00+08:00
expects_reply: false
---

verification body
```

要求动作：
1. 写入目标 repo 的 `inbox/<filename>.md`
2. `git add -- <file>`
3. `git commit -m "agm: verification mail"`

保留：
- 文件名
- commit SHA

---

## Step 5：观察 plugin / watcher 日志

目标：确认 plugin 确实检测到了这封新信，并尝试对 forced sessionKey 做 inject / wake。

至少要能看到其中几项：
- 检测到 `A inbox/*.md`
- 提取到 `filename`
- 提取到 `from`
- 记录 inject 目标 sessionKey
- 记录 request wake 动作

保留：
- 日志片段

---

## Step 6：观察当前飞书主会话是否收到提醒

目标：这是本轮最核心的成功标准。

期望：
- 当前这个飞书直聊主会话收到一条可见提醒
- 内容至少包含：
  - `from`
  - `filename`

类似：

```text
New agent git mail: from=hex, file=<filename>
```

保留：
- 会话里实际可见的提醒证据
- 如果没看到提醒，也要记录当时的日志与时间点

---

## 成功标准

本轮 PASS 需要同时满足：

1. plugin build 成功
2. plugin link 安装成功，且 `Status: loaded`
3. 新信被 watcher 检测到
4. 当前飞书主会话出现可见提醒

只满足 1~3，不算 PASS。

---

## 失败时的排查顺序

### 情况 1：plugin 没 loaded
先查：
- package build 产物
- `openclaw.plugin.json`
- `openclaw.extensions`
- export 入口形式

### 情况 2：loaded 了，但 watcher 没检测到新信
先查：
- repo 路径是否正确
- git 水位是否已建立
- commit 是否真的产生了 `A inbox/*.md`

### 情况 3：检测到了，但会话没有提醒
先查：
- forced sessionKey 是否填对
- inject 是否命中这个 sessionKey
- wake 是否发出
- 会话侧是否有可见 system event

---

## 对执行者（Hex）的输出要求

回信时至少包含：

1. build 输出
2. `openclaw plugins info openclaw-agent-git-mail` 输出
3. 测试信件文件名 + commit SHA
4. plugin / watcher 日志片段
5. 当前飞书主会话是否真的收到提醒
6. 结论：PASS / FAIL
7. 如果 FAIL，失败停在第几步
