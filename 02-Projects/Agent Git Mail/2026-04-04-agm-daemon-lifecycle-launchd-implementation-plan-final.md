---
title: AGM daemon lifecycle + launchd implementation plan (final)
date: 2026-04-04
tags:
  - agm
  - daemon
  - launchd
  - implementation-plan
  - agent-git-mail
---

# AGM daemon lifecycle + launchd Implementation Plan（Final）

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 为 AGM 增加可管理的 daemon lifecycle：保留 `daemon run` 前台运行语义，并在 macOS 上提供 `daemon start|stop|status`，由 launchd 托管后台生命周期。

**Architecture:** AGM 自身不做 fork/pid 守护进程管理器；`run` 仍然是核心执行语义，`start/stop/status` 作为 macOS 宿主控制面，围绕每个 profile 生成独立 launchd job（一个 profile 一个 label/plist/log 路径）。状态真相源唯一为 launchd。

**Tech Stack:** TypeScript, Node.js, AGM CLI, macOS launchd / launchctl, plist generation, profile-scoped state paths.

---

## 一、最终拍板

### 1.1 总体方向

这轮不是简单补一个 `stop` 命令，而是把 AGM daemon 从：

- `agm --profile <name> daemon`
- 裸前台长期循环
- 只能 `Ctrl+C` / `kill`

升级成：

```bash
agm --profile <name> daemon run
agm --profile <name> daemon start
agm --profile <name> daemon stop
agm --profile <name> daemon status
```

其中：
- `run` = 前台核心执行语义
- `start/stop/status` = macOS 宿主控制面
- macOS 上后台托管统一走 launchd
- AGM 不自己实现 fork/pid/daemonize

一句话：

> **让 AGM 做 profile 级控制面，让 launchd 做后台进程托管。**

---

### 1.2 必须坚持的硬约束

#### 约束 1：`run` 保留为核心执行语义
`agm --profile <name> daemon run` 必须继续可用，并保持：
- 前台运行
- `--once` 测试模式
- Docker / CI / Linux 可直接使用

#### 约束 2：后台托管统一走 launchd
在 macOS 上：
- `start`
- `stop`
- `status`

全部围绕 launchd 实现，不允许 AGM 自己造后台进程管理器。

#### 约束 3：状态真相源唯一为 launchd
AGM 不维护独立的“daemon 正在运行”状态文件作为真相源。

可以有辅助 metadata，但：
- running / stopped / installed / error 的真相必须来自 launchd

#### 约束 4：一个 profile 对应一个 launchd job
例如：
- `ai.agm.daemon.mt`
- `ai.agm.daemon.hex`

不得混用、共享或把多个 profile 放进一个 job。

#### 约束 5：stdout/stderr 日志进入 profile state 目录
例如：

```text
~/.config/agm/state/mt/daemon.stdout.log
~/.config/agm/state/mt/daemon.stderr.log
```

#### 约束 6：非 macOS 显式 unsupported
在非 macOS 平台：
- `daemon run` 仍然可用
- `daemon start/stop/status` 必须明确返回 unsupported
- 不能抛出低质量的 `launchctl not found`

---

## 二、最终对比与收口

这份 final 版是 MT 版与 Hex 版中和后的结果。

### 吸收 Hex 版的部分
- 6 个 Phase 拆分比较清楚
- `run-daemon.ts` 保持不动的判断是对的
- 倾向用较少文件完成闭环，这符合当前仓库复杂度
- 状态真相源唯一为 launchd，这点判断正确

### 吸收 MT 版的部分
- 更强调 CLI 语义分层：`run/start/stop/status`
- 更明确 AGM 与 launchd 的职责边界
- 更强调不做自管 pid/fork
- 更强调错误语义、幂等、跨平台降级策略
- 更强调日志与 state 路径模型

### 最终收口结论
- 保留 Hex 的“6 Phase 落地结构”
- 采用 MT 的“职责边界 / 错误语义 / 跨平台策略 / 状态真相模型”
- 实现上以较少新文件完成，但不牺牲语义清晰度

---

## 三、模块 / 文件改动清单

## 3.1 新建文件

### 核心新增
- Create: `packages/agm/src/cli/commands/daemon.ts`
- Create: `packages/agm/src/daemon/launchd.ts`

### 测试
- Create: `packages/agm/test/daemon-cli.test.ts`
- Create: `packages/agm/test/daemon-launchd.test.ts`
- Create: `packages/agm/test/daemon-status.test.ts`

> 说明：如果现有测试风格更适合合并到已有 `daemon.test.ts`，可以合并，但建议至少逻辑上区分 CLI action 测试和 launchd 语义测试。

---

## 3.2 修改文件

### CLI / 入口
- Modify: `packages/agm/src/index.ts`

### daemon 核心
- Modify: `packages/agm/src/app/run-daemon.ts` **（尽量不改逻辑，必要时仅改调用方式适配）**

### 路径
- Modify: `packages/agm/src/config/profile-paths.ts`

### 可选补充
- Modify: `packages/agm/src/config/index.ts`
- Modify: `packages/agm/src/config/profile.ts`

---

## 3.3 为什么只新增这两个核心文件

### `src/cli/commands/daemon.ts`
负责：
- `daemon` action 分发
- `run/start/stop/status` 命令语义
- CLI 错误处理
- 调 launchd 模块

### `src/daemon/launchd.ts`
负责：
- label 生成
- plist path 生成
- stdout/stderr path 生成
- plist 内容生成
- `launchctl` 调用封装
- `status` 所需 launchd 查询封装

> 这版不强制拆成 `daemon/paths.ts` / `daemon/plist.ts` / `daemon/status.ts` 三四个文件。当前规模下，两文件模型更贴近 AGM 现有代码风格，也更容易落地。

---

## 四、路径与命名模型

## 4.1 launchd label

每个 profile 一个独立 label：

```text
ai.agm.daemon.<profile>
```

例如：
- `ai.agm.daemon.mt`
- `ai.agm.daemon.hex`

必要时可对 profile 做最小 sanitize，但前提仍应是 profile 名本身已受 config 约束。

---

## 4.2 plist 路径

```text
~/Library/LaunchAgents/ai.agm.daemon.<profile>.plist
```

例如：

```text
~/Library/LaunchAgents/ai.agm.daemon.mt.plist
```

---

## 4.3 日志路径

```text
~/.config/agm/state/<profile>/daemon.stdout.log
~/.config/agm/state/<profile>/daemon.stderr.log
```

这些路径应通过 `profile-paths.ts` 提供统一生成函数，例如：

```ts
getDaemonStdoutPath(profile)
getDaemonStderrPath(profile)
```

---

## 4.4 ProgramArguments

launchd 最终执行的命令必须是：

```bash
agm --profile <name> daemon run
```

不要额外创造第二套后台入口。

这点必须写死：

> **run 是核心执行语义；start 只是托管方式。**

---

## 五、分阶段实施计划（最终版）

## Phase 1：daemon action 语义收口

**目标：** 把当前裸 `daemon` 子命令收成 `run/start/stop/status`。

### 任务
- [ ] 在 `packages/agm/src/index.ts` 中把 `daemon` handler 从“直接调 runDaemon”改成“调 `cmdDaemon(argv)`”
- [ ] 新建 `packages/agm/src/cli/commands/daemon.ts`
- [ ] 在 `cmdDaemon(argv)` 中解析 action：`run/start/stop/status`
- [ ] 无 action 时，短期兼容映射到 `run`
- [ ] `run` action 保持调用现有 `runDaemon(...)`
- [ ] `--once` 只归属于 `run`
- [ ] 更新 help/usage/example

### 验收
- `agm --profile mt daemon run` 正确工作
- `agm --profile mt daemon` 默认等价于 `daemon run`（如果保留兼容）
- `agm --profile mt daemon start|stop|status` 能被正确分发

### 提交粒度
- `feat(agm): add daemon run/start/stop/status actions`

---

## Phase 2：launchd label / 路径 / plist 生成器

**目标：** 先把 launchd 相关派生逻辑收成纯函数。

### 任务
- [ ] 新建 `packages/agm/src/daemon/launchd.ts`
- [ ] 实现 `getLaunchdLabel(profile)`
- [ ] 实现 `getLaunchdPlistPath(profile)`
- [ ] 在 `profile-paths.ts` 新增 `getDaemonStdoutPath(profile)`
- [ ] 在 `profile-paths.ts` 新增 `getDaemonStderrPath(profile)`
- [ ] 在 `launchd.ts` 实现 `generateLaunchdPlist(profile, opts)`
- [ ] plist 中包含：
  - Label
  - ProgramArguments
  - WorkingDirectory（如需要）
  - RunAtLoad
  - KeepAlive
  - StandardOutPath
  - StandardErrorPath
  - 必要 EnvironmentVariables（`HOME`, `PATH` 等）

### 关键约束
- plist 不是第二套配置源
- 不往 launchd plist 塞 daemon 专属业务配置
- stdout/stderr 必须用绝对路径

### 验收
- label / plist path / log path 生成正确
- ProgramArguments 指向 `agm --profile <name> daemon run`
- plist 内容可预测、可测试

### 提交粒度
- `feat(agm): add launchd plist and daemon path generators`

---

## Phase 3：`daemon start`

**目标：** 让 AGM 能为某个 profile 生成/更新 launchd job 并启动它。

### 任务
- [ ] 在 `launchd.ts` 实现 `installOrUpdateLaunchdJob(profile, ...)`
- [ ] 在 `launchd.ts` 实现 `startLaunchdJob(profile)`
- [ ] 在 `daemon.ts` 实现 `cmdDaemonStart(profile)`
- [ ] `start` 执行时：
  1. 校验 profile
  2. 确保 state 目录存在
  3. 写入/更新 plist
  4. 如果 job 已存在且在运行，返回 `already running`
  5. 否则调用 `launchctl bootstrap` / `kickstart`
- [ ] 输出清晰的成功/失败信息

### 关键行为
- 同一 profile 重复 `start` 应幂等或安全拒绝
- 不允许一个 profile 出现多个后台 job

### 验收
- plist 文件生成正确
- `launchctl` 可成功拉起 job
- 重复 `start` 不会重复起实例

### 提交粒度
- `feat(agm): implement daemon start via launchd`

---

## Phase 4：`daemon stop`

**目标：** 能优雅停掉某个 profile 的后台 daemon。

### 任务
- [ ] 在 `launchd.ts` 实现 `stopLaunchdJob(profile)`
- [ ] 在 `daemon.ts` 实现 `cmdDaemonStop(profile)`
- [ ] stop 逻辑调用 `launchctl bootout` 或等价停止路径
- [ ] 对下面几种情况提供幂等结果：
  - running → stopped
  - already stopped
  - not installed
- [ ] 默认**不删除 plist**（除非后续另设计 uninstall）

### 关键判断
- `stop` 的目标是停机，不是卸载配置
- “已停止/未安装”不应算致命错误

### 验收
- `daemon stop` 后 job 不再运行
- 重复 stop 不崩

### 提交粒度
- `feat(agm): implement daemon stop via launchd`

---

## Phase 5：`daemon status`

**目标：** 让用户能查询 profile daemon 当前状态。

### 任务
- [ ] 在 `launchd.ts` 实现 `queryLaunchdJob(profile)`
- [ ] 在 `daemon.ts` 实现 `cmdDaemonStatus(profile)`
- [ ] 以 launchd 查询结果为唯一真相源
- [ ] 输出至少包含：
  - profile
  - label
  - state: `running | stopped | not-installed | error`
  - plist path
  - stdout/stderr path
  - pid（如可取到）
- [ ] 可选支持 `--json`

### 关键约束
- 不新增 AGM 自己的 `daemon.running.json` 真相文件
- status 不是读 pid 文件，而是读 launchd 状态

### 验收
- running/stopped/not-installed/error 映射正确
- 输出对人类可读

### 提交粒度
- `feat(agm): add daemon status command`

---

## Phase 6：帮助文案 + 跨平台边界 + 回归验证

**目标：** 把 macOS 优先、run 保底这件事彻底说清楚，并保证老能力不被改坏。

### 任务
- [ ] 在 `daemon.ts` / `index.ts` 中对非 macOS 平台加显式 unsupported 处理
- [ ] 非 darwin 平台：
  - `daemon run` 仍可用
  - `daemon start/stop/status` 返回明确错误与提示
- [ ] 更新 help/usage/examples
- [ ] 保证 `run-daemon.ts` 逻辑不被 launchd 改坏
- [ ] 如果 `run-daemon.ts` 需要适配新调用方式，只做最小改动
- [ ] 补回归测试

### 建议错误文案
```text
error: daemon start/stop/status 仅在 macOS 上可用
hint: 在当前环境请使用 'agm --profile <name> daemon run'
```

### 验收
- Docker/CI/Linux 不会因为 launchd 新增逻辑坏掉
- `daemon run --once` 仍可正常工作

### 提交粒度
- `docs(agm): clarify daemon lifecycle and macOS launchd support`

---

## 六、测试计划（最终版）

## 6.1 CLI / action 解析
- [ ] `agm --profile mt daemon run` 正确解析
- [ ] `agm --profile mt daemon start` 正确解析
- [ ] `agm --profile mt daemon stop` 正确解析
- [ ] `agm --profile mt daemon status` 正确解析
- [ ] `agm --profile mt daemon` 默认映射到 `run`（如果保留兼容）
- [ ] `--once` 仅在 `run` 路径生效

## 6.2 label / plist / 路径生成
- [ ] `getLaunchdLabel('mt') === 'ai.agm.daemon.mt'`
- [ ] plist path 生成正确
- [ ] stdout/stderr path 生成正确
- [ ] ProgramArguments 指向 `daemon run`
- [ ] plist 内容包含关键字段

## 6.3 start / stop / status 逻辑
- [ ] `start` 生成正确 launchctl 调用
- [ ] 重复 `start` 返回 already running / 等价幂等语义
- [ ] `stop` 生成正确 launchctl 调用
- [ ] 重复 `stop` 返回 already stopped / not installed
- [ ] `status` 能映射 running/stopped/not-installed/error

## 6.4 非 macOS 边界
- [ ] `run` 仍可用
- [ ] `start/stop/status` 返回 unsupported
- [ ] 错误文案可读

## 6.5 回归测试
- [ ] `run-daemon.ts` 主逻辑不被破坏
- [ ] `daemon run --once` 仍可正常退出
- [ ] profile 路径隔离不被新日志路径污染

## 6.6 smoke / 集成验证（macOS）
这部分可以作为 smoke，不一定要求塞进纯单测：
- [ ] `start` 后 launchd job 可见
- [ ] `status` 显示 running
- [ ] `stop` 后 job 消失/停止
- [ ] stdout/stderr 文件落到 profile state 目录

---

## 七、风险与注意事项（最终版）

### 风险 1：把 AGM 重新做成后台进程管理器
如果实现过程中开始加：
- pid 文件作为真相源
- fork / daemonize
- 自管保活

就会和 launchd 边界冲突。必须避免。

### 风险 2：双真相源
如果同时维护：
- launchd 状态
- AGM 自己的 running 状态文件

后面一定会出现判断冲突。running 状态必须只认 launchd。

### 风险 3：CLI 语义不清
如果用户无法区分：
- `daemon run`
- `daemon start`

就说明帮助文案和默认行为没收好。help 必须明确。

### 风险 4：非 macOS 路径泄露低级错误
不能让用户看到一堆 `launchctl not found`。unsupported 必须在 AGM 层先拦住。

### 风险 5：stdout/stderr 路径不进 profile state
多 profile 下如果日志不进入 profile state，会直接增加运维复杂度。

### 风险 6：过早做太多宿主能力
第一版不要顺手扩：
- restart
- uninstall
- logs
- Linux systemd
- crash loop 高级治理

先把最小闭环做稳。

---

## 八、推荐执行顺序（最终）

采用如下顺序：

1. **先做 daemon action 分发**
2. **再做 label/path/plist 纯函数生成**
3. **先实现 `start`**
4. **再实现 `stop`**
5. **再实现 `status`**
6. **最后收口跨平台边界、help 和回归测试**

### 顺序理由
- 不先把 CLI action 拆出来，后续逻辑会继续糊在一起
- 不先把 label/path/plist 生成器抽出，start/stop/status 会复制逻辑
- status 依赖前面的路径/label/launchctl 查询模型先稳定
- 跨平台和文案最好最后统一收口

---

## 九、最终结论

这份 final 版实现计划融合了：

- **Hex 版**：6 个 Phase 结构更紧凑，`run-daemon.ts` 保持稳定边界的判断正确
- **MT 版**：CLI 语义、职责边界、错误语义、跨平台降级和状态真相模型更完整

最终 baseline 应以这份为准：

> **保留 `daemon run` 作为跨平台前台核心能力；在 macOS 上新增 `daemon start/stop/status`，统一通过 launchd 实现 profile-scoped 后台托管；状态真相源唯一为 launchd，AGM 不引入独立 pid/fork 后台管理器。**
