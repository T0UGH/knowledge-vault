# AGM Profile 模型设计

> 状态：Draft Final  
> 决策：采用 profile-first 架构；不保留旧的单实例配置模型

## 1. 目标

把 AGM 从当前的 **单实例 + 环境变量切换** 模型，升级为 **profile-first 的多 owner 模型**。

目标命令形态统一为：

```bash
agm --profile mt ...
agm --profile hex ...
```

系统必须支持多个独立 mailbox owner（例如 `mt`、`hex`）在同一台机器上共存，并且不能共享：

- self 身份
- mailbox ownership
- daemon 运行态
- checkpoint / event log / runtime state

---

## 2. 核心决策

### 2.1 Profile 是顶层身份单位

AGM 必须把 `profile` 作为一等公民的执行上下文。

一个 profile 代表：

- 一个 mailbox owner
- 一个 daemon 身份
- 一套 checkpoint 命名空间
- 一套 event/state 命名空间

例如：

- `mt`
- `hex`

AGM 后续不应再依赖：

- 隐式 shell 环境
- 默认 profile 推断
- 全局共享状态

---

### 2.2 一个 mailbox repo 只能有一个 owner

每个 self mailbox repo 只属于一个 profile owner。

例如：

- MT 的 mailbox repo 只属于 `mt`
- Hex 的 mailbox repo 只属于 `hex`

禁止两个 profile 把同一个 mailbox repo 当成自己的 `self` repo。

---

### 2.3 Contact 是 remote peer，不是共享本地 repo

contact 必须被建模为 remote peer。

即使 MT 和 Hex 跑在同一台机器上，AGM 也必须按“跨机 peer”来设计。

因此：

- contact 配置里只保留 `remote_repo_url`
- contact 配置不能直接指向对方的 self repo 路径
- 是否同机只是部署细节，不能改变 mailbox 协议语义

---

### 2.4 Contact 本地副本是 cache，不是权威 repo

如果 AGM 需要本地访问 contact mailbox，必须在当前 profile 自己的命名空间下维护一份 **contact cache**。

这份 cache：

- 首次使用时自动 clone
- 后续使用时 fetch / pull 更新
- 可以删除后重建
- 不能被视为 contact 的权威 owner repo

应写死这条规则：

> self repo 是 authoritative ownership state；contact cache 是 disposable local cache。

---

### 2.5 所有命令必须显式 `--profile`

所有 AGM 命令都必须显式传入 profile。

例如：

```bash
agm --profile mt send --to hex ...
agm --profile hex daemon
agm --profile mt doctor
agm --profile hex log
```

如果没有 `--profile`，必须直接失败。

不提供 default profile。  
不把环境变量作为默认主工作流。

---

## 3. 配置模型

### 3.1 新配置结构

AGM 应直接切换到统一的新 profile 配置格式：

```yaml
profiles:
  mt:
    self:
      id: mt
      remote_repo_url: https://github.com/agent-git-mail-group/mt-mail.git
    contacts:
      hex:
        remote_repo_url: https://github.com/agent-git-mail-group/hex-mail.git
    notifications:
      default_target: main
      bind_session_key: agent:main:feishu:direct:...
    runtime:
      poll_interval_seconds: 30
    host_integration:
      kind: happyclaw
      happyclaw:
        base_url: http://127.0.0.1:3000/internal
        bearer_token_env: HAPPYCLAW_INTERNAL_SECRET
        target_jid: feishu:oc_xxx

  hex:
    self:
      id: hex
      remote_repo_url: https://github.com/agent-git-mail-group/hex-mail.git
    contacts:
      mt:
        remote_repo_url: https://github.com/agent-git-mail-group/mt-mail.git
    notifications:
      default_target: main
    runtime:
      poll_interval_seconds: 30
    host_integration:
      kind: happyclaw
      happyclaw:
        base_url: http://127.0.0.1:3000/internal
        bearer_token_env: HAPPYCLAW_INTERNAL_SECRET
        target_jid: feishu:oc_xxx
```

---

### 3.2 配置规则

#### self

每个 profile 必须定义：

- `self.id`
- `self.remote_repo_url`

`self.id` 应与 profile 的语义身份一致。

#### contacts

每个 contact 只定义：

- `remote_repo_url`

不再暴露：

- `repo_path`
- `local_cache_path`

这些本地路径都应由 AGM 自动推导和管理。

#### notifications / runtime / host integration

这些配置都应是 profile-local 的，只能在选中的 profile 内部解析与生效。

---

## 4. 路径模型

### 4.1 self repo 路径

self repo 路径不建议继续让用户任意手填，建议由 AGM 统一派生：

```text
~/.agm/profiles/<profile>/self
```

例如：

```text
~/.agm/profiles/mt/self
~/.agm/profiles/hex/self
```

---

### 4.2 contact cache 路径

contact cache 路径也由 AGM 统一派生：

```text
~/.agm/profiles/<profile>/contacts/<contact>
```

例如：

```text
~/.agm/profiles/mt/contacts/hex
~/.agm/profiles/hex/contacts/mt
```

这样可以保证：

- 不直接复用 contact 的 owner repo
- ownership 与 cache 明确分离
- 同机与跨机的语义一致

---

### 4.3 状态目录

所有 runtime / state 文件都必须按 profile 隔离：

```text
~/.config/agm/state/<profile>/
```

例如：

```text
~/.config/agm/state/mt/activation-state.json
~/.config/agm/state/mt/events.jsonl
~/.config/agm/state/mt/session-bindings.json

~/.config/agm/state/hex/activation-state.json
~/.config/agm/state/hex/events.jsonl
~/.config/agm/state/hex/session-bindings.json
```

任何语义上绑定 profile 的状态文件，都不能继续全局共享。

---

## 5. CLI 语义

### 5.1 必须显式指定 profile

所有用户命令都必须带上 profile：

```bash
agm --profile mt send ...
agm --profile mt read ...
agm --profile mt list ...
agm --profile mt archive ...
agm --profile mt daemon
agm --profile mt doctor
agm --profile mt log
agm --profile mt bootstrap
```

---

### 5.2 未提供 profile 时的行为

如果没有传 `--profile`，AGM 必须报错。

建议错误信息：

```text
Missing required option: --profile

Example:
  agm --profile mt send --to hex ...
```

---

### 5.3 不允许隐式 default profile

AGM 后续不应：

- 自动选择 default profile
- 靠环境变量偷偷补默认值
- 从当前工作目录猜 profile

这是一个明确的安全决策，用来防止 cross-profile 误操作。

---

## 6. 运行时语义

### 6.1 daemon

例如：

```bash
agm --profile mt daemon
```

行为应为：

- 只加载 `profiles.mt`
- 只 watch MT 的 self repo
- 只写 MT 的 activation state
- 只写 MT 的 event log
- 只使用 MT 的 host integration

Hex daemon 同理，在自己的 profile 命名空间内运行。

不同 profile 的 daemon 必须完全隔离。

---

### 6.2 doctor

例如：

```bash
agm --profile hex doctor
```

只检查 Hex 这套 profile：

- self repo
- contact cache
- activation state
- event log
- runtime config
- host integration config

---

### 6.3 log

例如：

```bash
agm --profile mt log
```

只展示 MT 自己的事件流，不默认做 cross-profile 聚合。

---

## 7. Contact Cache 生命周期

### 7.1 首次使用

第一次访问 contact 时：

- 自动推导 cache 路径
- 自动 clone remote repo 到该路径

---

### 7.2 后续使用

后续访问同一个 contact 时：

- 在同一个 cache 路径上 fetch / pull 更新
- 持续复用该 contact cache

---

### 7.3 ownership 边界

contact cache 只是实现细节。

它不能被误认为：

- contact ownership
- contact 的 self repo
- 共享 live working directory

---

## 8. 为什么这个设计是对的

### 8.1 比环境变量切换更好

环境变量切换在工程上“能跑”，但不是一个长期正确的产品模型。

它的主要问题：

- 上下文隐藏
- shell 残留污染
- 容易误用
- agent 数量一多就混乱

profile-first 让身份显式出现在命令语义里。

---

### 8.2 比共享 self repo 更好

如果多个 profile 共用一个 self repo，会直接破坏：

- daemon ownership
- waterline 跟踪
- checkpoint 归属
- event history
- activation routing

ownership 必须保持 singular。

---

### 8.3 比直接使用对方本地 repo 更好

如果一个 profile 可以直接指向另一个 profile 的 self repo，会让下面这些边界变脏：

- authority
- working tree ownership
- git 操作边界
- 同机与跨机语义

只允许 remote peer + 本地 cache，模型才是干净的。

---

## 9. 硬约束

以下规则应视为硬约束：

1. **所有 AGM 命令都必须显式 `--profile`**
2. **每个 self repo 只能归一个 profile owner**
3. **contact 不允许直接引用其他 profile 的 self repo 路径**
4. **contact cache 路径必须由 AGM 自动推导，不允许用户手写**
5. **state / log / checkpoint / daemon runtime 必须全部按 profile 隔离**
6. **不需要兼容旧的单实例模型**
7. **同机部署必须保持与跨机一致的 mailbox 语义**

---

## 10. 推荐下一步

基于这份设计，AGM 后续实现至少应覆盖：

- 新 config schema
- profile resolver
- self/contact/state 的 path resolver
- CLI 全局 `--profile` 解析
- daemon / doctor / log / bootstrap 的 profile 化
- 移除旧的单实例假设

---

## 11. 最终建议

正式采用 AGM 的 profile-first 模型作为后续 baseline。

一句话总结：

> AGM 应演进成一个多 profile mailbox 系统：MT 与 Hex 是两个独立 owner profile；contact 只认 remote；本地 contact repo 只是 cache；所有命令都必须显式 `--profile`。
