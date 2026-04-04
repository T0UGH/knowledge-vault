---
title: AGM daemon lifecycle + launchd 管理设计
date: 2026-04-04
tags:
  - agm
  - daemon
  - launchd
  - design
  - agent-git-mail
---

# AGM daemon lifecycle + launchd 管理设计

> 状态：Draft Final  
> 目标：把 AGM 的 daemon 从“裸前台循环命令”升级成“可管理的 profile-scoped daemon 生命周期接口”，并在 macOS 上用 launchd 作为后台托管机制。

## 1. 背景

当前 AGM 的 `daemon` 设计本质上只是：

- `agm --profile <name> daemon`
- 前台启动一个长期轮询循环
- `--once` 作为一次性 poll 测试模式

这套模型的问题是：

- 没有 `start`
- 没有 `stop`
- 没有 `status`
- 没有 profile 级后台生命周期管理
- 没有单实例约束
- 想停 daemon 只能靠 `Ctrl+C` 或手工 `kill`

这对于 profile-first AGM 来说已经不够了。

当前 AGM 已经支持：
- 多 profile
- self repo / contact cache / state 按 profile 隔离
- Docker E2E

但 daemon 仍然停留在“裸 runner”阶段，因此需要补齐 lifecycle 设计。

---

## 2. 设计结论

## 2.1 总体结论

把 AGM daemon 设计成两层：

### 层 1：核心运行层
保留：

```bash
agm --profile mt daemon run
```

语义：
- 前台运行
- 不负责后台托管
- 主要用于：
  - 调试
  - Docker
  - CI
  - 非 macOS 环境

### 层 2：宿主管理层
在 macOS 上新增：

```bash
agm --profile mt daemon start
agm --profile mt daemon stop
agm --profile mt daemon status
```

语义：
- 不自己 fork / daemonize
- 不自己管理后台 pid 树
- 由 AGM 生成和管理 profile 对应的 launchd job
- 通过 `launchctl` 完成后台启动/停止/状态查询

一句话：

> **前台运行保留在 AGM 自身；后台生命周期交给 launchd；AGM 提供统一控制面。**

---

## 2.2 为什么选 launchd

相比 AGM 自己实现一套后台进程管理（fork/pid/signal/保活），launchd 更合适。

### 原因 1：更符合 macOS 生态
贵平这台机器是 macOS，长期后台任务的标准托管机制本来就是 launchd。

### 原因 2：避免重复造后台管理轮子
如果 AGM 自己做后台化，需要额外处理：
- fork
- pid 文件
- signal 清理
- 崩溃恢复
- 单实例保护
- stdout/stderr 管理

这些都属于典型“重复发明宿主能力”。

### 原因 3：更适合 profile-first 模型
一个 profile 一个 launchd job，天然清楚：

- `mt` 跑一个 daemon
- `hex` 跑一个 daemon
- 互不混淆

### 原因 4：仍然保留跨平台核心能力
因为 `daemon run` 仍然保留，所以：
- Docker/CI 场景不受影响
- Linux 未来可以再接 systemd
- 不把 AGM 核心逻辑绑死在 launchd 上

---

## 3. 命令模型

## 3.1 新命令形态

建议把当前 daemon 子命令收口为：

```bash
agm --profile <name> daemon run
agm --profile <name> daemon start
agm --profile <name> daemon stop
agm --profile <name> daemon status
```

### `run`
- 前台运行
- 等价于当前 daemon 主行为
- 可以保留 `--once`

示例：
```bash
agm --profile mt daemon run
agm --profile mt daemon run --once
```

### `start`
- 生成/更新当前 profile 对应的 launchd plist
- 用 `launchctl bootstrap` / `kickstart` 启动
- 若已在运行，返回“已运行”或安全拒绝重复启动

### `stop`
- 停止当前 profile 对应的 launchd job
- 不要求用户手动找 pid
- 停止后状态应可查询为 stopped

### `status`
- 查询当前 profile daemon 状态
- 至少返回：
  - running
  - stopped
  - error / unknown

---

## 3.2 不建议保留的旧形态

不建议继续维持：

```bash
agm --profile mt daemon
```

原因：
- 语义太含混
- 容易分不清是前台 run 还是后台 start
- profile-first 以后生命周期命令应该更明确

建议：
- 要么把无 action 的 `daemon` 视为 `daemon run`
- 要么直接要求显式 action

我更倾向：

> **长期要求显式 action：run / start / stop / status。**

如果短期要兼容旧习惯，可以让：
- `daemon` = `daemon run`

但帮助文档里应优先展示新模型。

---

## 4. launchd 设计

## 4.1 job label

每个 profile 一个独立 label：

```text
ai.agm.daemon.mt
ai.agm.daemon.hex
```

规则：
- 前缀固定：`ai.agm.daemon.`
- 后缀为 profile 名称

这样：
- label 可预测
- status/stop/start 查询简单
- 多 profile 共存不混淆

---

## 4.2 plist 路径

建议放在：

```text
~/Library/LaunchAgents/ai.agm.daemon.mt.plist
~/Library/LaunchAgents/ai.agm.daemon.hex.plist
```

这是标准的用户级 launch agent 位置。

说明：
- AGM 的 daemon 是用户态后台任务
- 不需要上升到系统级 LaunchDaemons

---

## 4.3 ProgramArguments

launchd 实际执行的命令建议为：

```bash
agm --profile mt daemon run
```

也就是说：
- `start` 不是自己另起一套后台模式
- `start` 只是把 `daemon run` 放进 launchd 托管

这是非常关键的分层：

> **run 是核心执行语义；start 是托管方式。**

---

## 4.4 EnvironmentVariables

launchd job 里需要携带：
- `HOME`
- `PATH`
- 可能的 CA 相关环境
- profile 对应需要的运行环境变量

但 AGM 自身状态（如 self repo/state 路径）仍然应该优先从 config/profile 路径解析，而不是塞一堆 daemon 专属环境变量。

不要让 launchd plist 变成“第二套配置源”。

---

## 4.5 标准输出和错误日志

建议放到 profile state 目录：

```text
~/.config/agm/state/mt/daemon.stdout.log
~/.config/agm/state/mt/daemon.stderr.log

~/.config/agm/state/hex/daemon.stdout.log
~/.config/agm/state/hex/daemon.stderr.log
```

理由：
- 与 profile state 对齐
- 更方便 `status` / 故障排查
- 不污染全局日志位置

---

## 5. 状态模型

## 5.1 状态来源

在 launchd 模型下，状态的真相源应为：

- launchd job 是否已注册
- launchd job 当前是否运行

AGM 不应再自己额外维护一套“daemon 正在运行”的独立真相源。

否则会出现：
- AGM 觉得运行中
- launchd 实际没跑

这类双真相问题。

---

## 5.2 `status` 输出建议

建议 `agm --profile mt daemon status` 输出至少包含：

- profile 名
- launchd label
- state: `running | stopped | error | not-installed`
- plist 路径
- stdout/stderr 路径
- 若运行中，可附加 pid（如果 launchctl 可取到）

例如：

```text
profile: mt
label: ai.agm.daemon.mt
state: running
plist: /Users/wangguiping/Library/LaunchAgents/ai.agm.daemon.mt.plist
stdout: /Users/wangguiping/.config/agm/state/mt/daemon.stdout.log
stderr: /Users/wangguiping/.config/agm/state/mt/daemon.stderr.log
pid: 12345
```

---

## 5.3 单实例约束

单实例约束应基于 launchd label，而不是 pid 文件。

同一个 profile：
- 只能有一个对应 launchd job
- `start` 时如果已经存在运行实例，应返回：
  - already running
  - 或已安装并已加载

不需要另外造 `daemon.pid` 作为主控制手段。

如果后续需要辅助调试信息，可以有一个 metadata 文件，但它不是状态真相源。

---

## 6. AGM 负责什么，launchd 负责什么

## 6.1 AGM 负责

- profile 解析
- launchd label 生成
- plist 内容生成
- plist 写入/更新
- `start/stop/status` 命令语义
- 日志路径与 state 路径约定
- 错误提示与状态展示

## 6.2 launchd 负责

- 真正拉起进程
- 停止进程
- 托管生命周期
- 崩溃恢复（如配置 KeepAlive）
- 进程 stdout/stderr 重定向

这个边界非常重要：

> **AGM 不再自己实现后台化，只做“对 launchd 的 profile 级控制面”。**

---

## 7. KeepAlive 策略

## 7.1 默认策略

我建议第一版使用：

- `RunAtLoad = true`
- `KeepAlive = true`

原因：
- daemon 的语义本来就是长期轮询
- profile daemon 一般希望常驻

但要注意：
- 如果配置错误导致进程瞬间退出，launchd 可能反复重启
- 所以后续可以补：
  - 最小重启节流
  - 状态提示里显示近期崩溃信息

## 7.2 第一版不要做太复杂

先不做：
- 太细粒度的 restart policy 配置
- daemon 自动禁用逻辑
- 复杂 crash loop 检测

第一版目标是：
- 能启动
- 能停止
- 能查状态
- 能稳定托管一个 profile daemon

---

## 8. 与 Docker / CI 的关系

## 8.1 Docker / CI 不走 launchd

Docker / CI 场景应继续使用：

```bash
agm --profile <name> daemon run
agm --profile <name> daemon run --once
```

不应该强行把 launchd 逻辑带进去。

## 8.2 为什么要保留 `run`

保留 `run` 的价值：
- 跨平台
- 适合容器
- 适合调试
- 适合测试
- 让后台控制与执行逻辑解耦

所以最终模型不是“start 取代 run”，而是：

- `run` = 执行核心
- `start/stop/status` = macOS 宿主集成层

---

## 9. 错误处理与用户体验

## 9.1 `start`

如果：
- profile 不存在
- plist 写入失败
- `launchctl bootstrap` 失败

应给出明确错误：
- profile 名
- plist 路径
- launchd label
- 原始错误摘要

## 9.2 `stop`

如果：
- job 不存在
- job 已停止

应返回幂等结果，例如：
- already stopped
- not installed

而不是把它当成致命错误。

## 9.3 `status`

status 不应该只给 exit code。  
应该优先给人类可读结果。

必要时可补：
- `--json`

供脚本调用。

---

## 10. 推荐实施顺序

建议按下面顺序推进：

1. 抽出 daemon sub-action 语义：`run/start/stop/status`
2. 保持 `run` 语义不变
3. 新增 launchd label / plist path 生成器
4. 实现 `start`
5. 实现 `stop`
6. 实现 `status`
7. 补 macOS 专项测试 / smoke test
8. 最后决定是否保留旧 `daemon` → `run` 兼容入口

---

## 11. 最终建议

正式采用如下模型：

> **AGM daemon lifecycle 分成两层：`daemon run` 作为前台核心执行语义；`daemon start/stop/status` 在 macOS 上通过 launchd 实现 profile-scoped 后台托管。**

一句话总结：

> **不要让 AGM 自己变成一个后台进程管理器；让 AGM 做 profile 级控制面，让 launchd 做后台进程托管。**
