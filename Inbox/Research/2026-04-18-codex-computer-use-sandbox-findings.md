---
title: Codex CLI computer use 沙箱兼容性实测结论
tags:
  - hermes
  - codex
  - computer-use
  - sandbox
---

# 结论

- **Codex CLI 的 computer use 能跑通。**
- 但这次实测表明：**普通 sandbox 模式下不稳定甚至不可用，`danger-full-access` 下可用。**
- 真正的阻塞点不是 `computer-use@openai-bundled` 是否存在，也不是本机 `agent-browser` 工具坏掉，而是 **Codex 的命令执行沙箱 / 本地 daemon 运行模型** 与这类 browser automation 工具存在兼容性问题。

# 核心判断

## 1. 能力面是存在的

通过 Codex CLI 发起最小无副作用 smoke test，目标仅为：

- 打开 `about:blank`
- 读取 URL
- 读取 title
- 做页面快照
- 关闭 browser session

在 `danger-full-access` 模式下，以上动作完整成功，说明：

- Codex CLI 可以调起本机可用的 browser automation 能力
- `agent-browser` 在 Codex 体系下不是“理论存在”，而是**实测可跑**

## 2. 默认/受限 sandbox 模式是主要问题

### read-only

表现：直接失败。

典型原因：
- socket/state/runtime 目录不可写
- 本地 browser daemon 无法初始化

### workspace-write

表现：比 read-only 前进了一步，但仍未稳定跑通。

这轮暴露了两层问题：

1. 初始阶段有 **socket 路径长度** 问题
2. 即便将 `AGENT_BROWSER_SOCKET_DIR` 缩短到 `/tmp/cabs`，仍出现：
   - `Daemon failed to start (socket: /tmp/cabs/default.sock)`

这说明问题不只是目录权限或路径长度，**更像是 Codex sandbox 对该 daemon/子进程模型支持不完整。**

### danger-full-access

表现：完整通过。

成功执行了：
- `agent-browser open about:blank`
- `agent-browser get url` -> `about:blank`
- `agent-browser get title` -> 空标题（符合预期）
- `agent-browser snapshot -i` -> `(no interactive elements)`
- `agent-browser close` -> `✓ Browser closed`

# 对照实验结果

## A. 本机直接跑 agent-browser

在 shell 中直接执行，并显式设置短路径：

- `AGENT_BROWSER_HOME=/tmp/cabh`
- `AGENT_BROWSER_SOCKET_DIR=/tmp/cabs`

结果：**成功**。

这说明：
- `agent-browser` 本身是好的
- 本机环境本身不是根本问题
- 问题主要出在 **Codex 如何承载这个工具**

## B. Codex + read-only

结果：**失败**。

原因集中在：
- 不可写 runtime/socket/cache

## C. Codex + workspace-write

结果：**失败**。

即使补了：
- 可写目录
- 短 socket 路径

仍然无法稳定启动 daemon。

## D. Codex + danger-full-access

结果：**PASS**。

这是当前最关键的实证：

> **Codex 的 computer use 不是完全不能用，而是在 sandbox 模式下容易被卡死；放开沙箱后可用。**

# 对 Hermes 接入的启发

## 1. 不要把问题误判为“插件本体缺失”

前面虽然公开仓里找不到 `computer-use@openai-bundled` 本体，但从实测看，真正影响可用性的更大问题不是“有没有这个名字对应的公开 plugin”，而是：

- 本地 computer-use 工具往往依赖 daemon / socket / cache / browser runtime
- 这类工具对 sandbox 约束非常敏感

## 2. Hermes 如果接这类能力，优先考虑“工具直接跑通”而不是“在 agent sandbox 里强封装”

更现实的思路是：

- 先保证外部 browser/desktop automation 工具在宿主机上可稳定运行
- 再由 Hermes 通过 tool/MCP 做编排
- 不要一开始就把它塞进一个过严的 command sandbox 里，否则会复现 Codex 这次的问题

## 3. browser automation 与 desktop automation 要分开评估

这次跑通的是 `agent-browser` 路线。

而 `uvx desktop-agent` 这条路线，在 Codex 里仍被 cache/temp path 卡住，说明：

- browser automation 可行 ≠ desktop automation 同样可行
- 后者对本地运行环境的依赖更重，风险更高

# 推荐结论

当前可以下的工程判断是：

1. **Codex CLI 的 computer use 是可用的**
2. **但对 sandbox 非常敏感**
3. **如果要在 Hermes 里承接类似能力，优先复用可直接运行的 browser automation tool，而不是照搬 Codex 的 sandbox 模式**
4. **Hermes 后续若接 desktop-control，需要单独验证 daemon/socket/cache/runtime 这一层，不要只看 prompt 或 tool schema**

# 验证证据

本次实测关键结果：

- `read-only`：FAIL
- `workspace-write`：FAIL
- `danger-full-access`：PASS

通过动作：
- open `about:blank`
- get url
- get title
- snapshot
- close

失败特征包括：
- socket/state 不可写
- socket daemon start fail
- uv/desktop-agent 临时文件与 cache 初始化失败

# 下一步建议

如果继续验证，最有价值的下一步是：

1. 用 `danger-full-access` 再测一次真实但无副作用网页（如 `https://example.com`）
2. 单独验证 screenshot 能否稳定产出文件
3. 若要推进 Hermes 接入，优先做 `agent-browser` 路线的最小接入方案，而不是直接追 Codex 私有 plugin 本体
