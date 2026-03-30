# 2026-03-30 AGM Bootstrap 技术实现方向（MVP 增补版）

## 目标

这份文档用于指导实现一个 **非交互式、幂等、对运行中的 OpenClaw 尽量无害** 的 AGM bootstrap 方案。

目标不是做万能安装器，而是把系统推进到：

> **最小可用（minimal-ready）**：至少能收通知，而且不会把用户当前正在运行的 OpenClaw 杀死。

本文刻意写得偏细，目标是让一个不太聪明的 AI 也能照着分步实现，而不是靠大量隐含判断。

---

## 一、明确目标边界

## 1.1 要做到什么

bootstrap 完成后，系统应满足：

1. 系统环境已满足 AGM 运行前置条件
2. `@t0u9h/agent-git-mail` 已安装
3. `@t0u9h/openclaw-agent-git-mail` plugin 已安装
4. AGM 的最小配置文件已存在且合法
5. 默认通知路由为 `main`
6. 插件后续运行时，至少具备“收通知”的最小能力

## 1.2 明确不做什么

第一版 bootstrap **不负责**：

1. 自动安装系统依赖（如 `git` / `node` / `openclaw`）
2. 自动 clone mailbox repo
3. 自动创建 remote repo
4. 自动初始化联系人/通讯录（contacts）
5. 自动发现所有 session
6. 自动 restart/reload OpenClaw/gateway
7. 自动修复复杂配置冲突

---

## 二、推荐架构：两层实现

推荐方案：

1. 一个很薄的 `bootstrap.sh`
2. 一个真正负责逻辑的 `agm bootstrap` 子命令

即：

```text
bootstrap.sh
  -> 检查系统环境
  -> 安装 agm CLI
  -> 调用 agm bootstrap

agm bootstrap
  -> 校验输入
  -> 校验 mailbox repo
  -> 安装 plugin（如果缺失）
  -> 写 AGM 最小配置
  -> 返回机器可读结果
```

### 为什么不要把所有逻辑都塞进 shell

因为 shell 不擅长：
- 配置合并
- 路径与状态校验
- 幂等逻辑
- 结构化输出
- 复杂错误处理

而 Node/TS CLI 更适合做这些事。

所以原则是：

> **shell 只做入口和环境检查；真正的初始化逻辑放进 `agm bootstrap`。**

---

## 三、bootstrap.sh 的职责

## 3.1 输入方式

必须是 **非交互式**。

建议支持以下环境变量：

```bash
AGM_SELF_ID=mt
AGM_SELF_REPO_PATH=/path/to/my-mailbox
AGM_CONFIG_PATH=/custom/path/config.yaml   # 可选
AGM_INSTALL_PLUGIN=1                       # 可选，默认 1
```

最小必须有：
- `AGM_SELF_ID`
- `AGM_SELF_REPO_PATH`

## 3.2 bootstrap.sh 只做这 4 件事

### Step 1：检查关键系统依赖

检查以下命令是否存在：
- `git`
- `node`
- `npm`
- `openclaw`

如果缺任何一个：
- 打明确错误
- exit non-zero

**不要尝试自动安装这些系统依赖。**

### Step 2：安装 AGM CLI

执行：

```bash
npm install -g @t0u9h/agent-git-mail
```

如果失败：
- 打明确错误
- exit non-zero

### Step 3：调用 `agm bootstrap`

例如：

```bash
agm bootstrap \
  --self-id "$AGM_SELF_ID" \
  --self-repo-path "$AGM_SELF_REPO_PATH" \
  --config-path "$AGM_CONFIG_PATH"
```

### Step 4：原样透传退出码

- `agm bootstrap` 成功 → shell 成功退出
- `agm bootstrap` 失败 → shell 失败退出

## 3.3 shell 脚本明确禁止做的事

bootstrap.sh 不允许：
- 直接写 YAML 配置内容
- 直接操作 OpenClaw 主配置 JSON
- 直接 restart/reload gateway
- 自动 kill 当前 OpenClaw 进程
- 在 shell 中实现复杂幂等逻辑

---

## 四、`agm bootstrap` 的职责

这是核心实现点。

## 4.1 新增命令

在 AGM CLI 中新增子命令：

```bash
agm bootstrap
```

建议支持参数：

```bash
agm bootstrap \
  --self-id <id> \
  --self-repo-path <path> \
  [--config-path <path>] \
  [--skip-plugin-install] \
  [--dry-run] \
  [--json]
```

## 4.2 命令输入校验

至少校验：

### 必填项
- `self-id` 非空
- `self-repo-path` 非空

### 路径合法性
- `self-repo-path` 存在
- `self-repo-path/.git` 存在，或 `git -C <path> rev-parse --git-dir` 成功

### config-path
- 如果不传，则用默认 AGM 配置路径
- 如果传了，则用传入路径

---

## 五、配置模型（MVP 增补版）

第一版建议配置文件从旧的：

```yaml
agents:
  mt:
    repo_path: /path/to/mt
  hex:
    repo_path: /path/to/hex
```

演进为：

```yaml
self:
  id: mt
  repo_path: /path/to/my-mailbox

notifications:
  default_target: main
  forced_session_key: null

runtime:
  poll_interval_seconds: 30
```

## 5.1 为什么先不写 contacts

这轮 bootstrap 的目标是：
- 至少能收通知
- 不要求立刻能给别人发信

所以当前不把 `contacts` 加进 bootstrap 必须项。

后续可在下一轮补：

```yaml
contacts:
  hex:
    repo_path: /path/to/hex-mailbox
```

但这不是第一版 bootstrap 的要求。

## 5.2 为什么要引入 `self`

因为当前 `agents:` 模型看不出“我是谁”。

引入 `self` 后，配置至少能表达：
- 当前安装实例是谁
- 当前实例自己的 mailbox repo 在哪

这是让 CLI 语义成立的第一步。

---

## 六、plugin 安装策略

## 6.1 `agm bootstrap` 应负责 plugin 安装

推荐做法：

如果没有传 `--skip-plugin-install`，则执行：

```bash
openclaw plugins install @t0u9h/openclaw-agent-git-mail
```

### 成功
继续执行

### 失败
明确报错并退出

## 6.2 但不负责 restart/reload

这里非常关键：

bootstrap 可以安装 plugin，但 **不能自动重启 OpenClaw**。

原因：
- 这会影响当前运行中的会话
- 有明显“自杀式安装”风险

所以正确行为应是：
- 安装 plugin
- 写配置
- 输出说明：已安装完成，运行时将在后续加载/下一次合适时机生效

---

## 七、幂等与 self-safe 约束

这是第一版实现最关键的工程纪律。

## 7.1 幂等（idempotent）要求

重复执行 `bootstrap.sh` / `agm bootstrap` 时，应满足：

### 情况 A：系统已正确初始化
输出：
- success / no-op
- 不重复破坏配置
- 不重复安装到异常状态

### 情况 B：部分初始化
例如：
- agm 已安装
- plugin 未安装
- config 未写

则只补缺失部分。

### 情况 C：配置冲突
例如：
- 当前配置里 `self.id = mt`
- 本次输入 `--self-id hex`

则：
- 明确报冲突
- 退出失败
- **不要自动覆盖现有配置**

## 7.2 self-safe 要求

bootstrap 不应导致当前运行中的 OpenClaw 明显降级或中断。

必须满足：

1. 不自动 restart/reload OpenClaw/gateway
2. 不 kill 当前进程
3. 不重写整个主配置
4. 不清空已有 AGM 配置
5. 已初始化时尽量走 no-op

一句话：

> **bootstrap 必须更像无害初始化器，而不是强制控制器。**

---

## 八、运行时 guardrail（插件侧）

即使 bootstrap 存在，也不能完全假设环境永远正确。

plugin 侧仍需要一个**极薄 preflight**。

## 8.1 preflight 检查内容

每轮 poll 前，至少检查：
- AGM 配置能否加载
- `self.repo_path` 是否存在
- `self.repo_path` 是否是 git repo

## 8.2 preflight 失败行为

失败时：
- 不 crash 整个 daemon
- 不 silent success
- 打明确 warning
- 当前这轮跳过该 source
- 下轮继续尝试

日志示例：

```text
[agm] stage=preflight_failed reason=missing_self_repo_path
[agm] stage=preflight_failed reason=repo_not_found path=/xxx
[agm] stage=preflight_failed reason=not_a_git_repo path=/xxx
```

目的不是自修复，而是：
> **明确暴露不可用原因，让 agent/用户知道下一步该补什么配置。**

---

## 九、路由策略（与当前实现对齐）

当前 MVP 已经做了以下路由优先级：

1. 配了 `AGM_FORCED_SESSION_KEY` → 用显式配置
2. 否则如果有 runtime binding → 用 binding
3. 再否则默认走：

```text
agent:main:main
```

bootstrap 第一版不需要再碰复杂路由，只要保证：

```yaml
notifications:
  default_target: main
```

即可。

这与当前已验证通过的路径一致。

---

## 十、输出与退出语义

## 10.1 推荐支持 `--json`

`agm bootstrap --json` 输出：

```json
{
  "ok": true,
  "status": "initialized",
  "self": {
    "id": "mt",
    "repo_path": "/path/to/my-mailbox"
  },
  "configPath": "/Users/foo/.openclaw/agent-git-mail/config.yaml",
  "pluginInstalled": true,
  "defaultTarget": "main"
}
```

## 10.2 推荐状态枚举

- `initialized`：首次完成
- `already_initialized`：重复执行，无需变更
- `partially_repaired`：补了缺失项
- `conflict`：发现冲突，失败退出
- `invalid_input`：输入不合法，失败退出
- `environment_missing`：环境前置不满足，失败退出

## 10.3 退出码建议

- `0`：成功（包括 no-op）
- `2`：输入错误
- `3`：环境缺失
- `4`：repo 非法
- `5`：plugin 安装失败
- `6`：配置冲突

---

## 十一、推荐实现顺序

### Milestone 1：设计与 dry-run
实现：
- `agm bootstrap --dry-run`
- 只检查和打印计划
- 不落盘

目标：
- 先把输入、校验、输出语义做稳

### Milestone 2：真实 bootstrap
实现：
- plugin 安装
- AGM 配置写入
- 幂等 no-op

目标：
- 实现最小可用初始化

### Milestone 3：plugin 侧薄 preflight
实现：
- 配置缺失/路径错误时明确 warning
- 当前轮跳过

目标：
- 避免 silent half-dead 状态

### Milestone 4：文档与验收
补：
- README 安装节
- bootstrap 用法
- minimal-ready 定义

---

## 十二、需要特别提醒实现者的坑

1. **不要把 shell 变成配置系统**
2. **不要自动 restart OpenClaw**
3. **不要粗暴覆盖已有 AGM 配置**
4. **不要把 contacts 初始化塞进第一版 bootstrap**
5. **不要让 plugin preflight 失败导致整个 daemon 停止**
6. **不要把“日志里有 enqueue”误当成“用户已看见通知”**

---

## 十三、一句话总结

第一版技术实现应遵守这句总原则：

> **用一个很薄的 shell 做入口，用 `agm bootstrap` 做真正初始化；目标是把系统安全、幂等地推进到“至少能收通知”的最小可用状态，而不是做一套会自杀的万能安装器。**
