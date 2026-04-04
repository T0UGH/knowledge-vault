# AGM Profile 模型 Implementation Plan（Final）

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 把 AGM 从当前单实例/环境变量切换模型，升级为强制显式 `--profile` 的多 profile mailbox 模型，并让 MT / Hex 作为两个独立 owner profile 共存。

**Architecture:** 以 `profile` 作为 AGM 顶层执行上下文，重构 config schema、profile resolver、path resolver、state/log/checkpoint 目录、CLI 参数解析与 daemon/runtime 作用域。self repo、contact cache、runtime state 全部按 profile 隔离；contacts 只认 `remote_repo_url`，本地 cache 路径由 AGM 自动派生。

**Tech Stack:** TypeScript, Node.js, YAML config, Git-based mailbox repos, AGM CLI/runtime.

---

## 一、最终拍板

## 1.1 总体方向

这轮不再把 AGM 理解成：

- 单实例配置 + 通过 `AGM_CONFIG_DIR` 或 shell 环境临时切换身份

而是正式收口成：

- **AGM = profile-first mailbox runtime**
- **profile = 顶层执行上下文**
- **self repo / contact cache / daemon / checkpoint / event log 全部按 profile 隔离**

一句话：

> **把 AGM 从“运维层切实例”升级成“产品层显式 profile 模型”。**

## 1.2 必须坚持的 6 条硬约束

### 约束 1：所有命令必须显式 `--profile`
- `agm --profile mt ...`
- `agm --profile hex ...`
- 不提供 default profile
- 缺失 `--profile` 直接报错

### 约束 2：一个 mailbox repo 只能对应一个 self owner
- `mt` mailbox repo 只归 `mt`
- `hex` mailbox repo 只归 `hex`
- 禁止两个 profile 把同一个 repo 当成自己的 self repo

### 约束 3：contacts 只认 `remote_repo_url`
- 不允许 `contacts.<id>.repo_path`
- 不允许 `contacts.<id>.local_cache_path`
- 即使同机，也按 remote peer 语义处理

### 约束 4：contact 本地副本是 profile-scoped cache，不是对方本体 repo
- 首次使用自动 clone
- 后续自动 fetch/pull
- cache 路径由 AGM 自动派生
- cache 是 disposable local cache，不是 ownership state

### 约束 5：state / log / checkpoint / daemon runtime 必须按 profile 隔离
- `activation-state.json`
- `events.jsonl`
- `session-bindings.json`
- daemon 运行态与 doctor/log 视图
- 全部切到 `state/<profile>/...`

### 约束 6：不兼容旧单实例模型
- 不保留 V2 / legacy fallback
- 不做自动兼容加载
- 不把迁移脚本纳入本轮正式范围

---

## 二、仓库边界与代码现实

## 2.1 真实代码结构（以当前 AGM repo 为准）

后续 implementation plan 必须贴 AGM 当前真实路径，不按抽象旧结构写。核心路径包括：

### Config
- `packages/agm/src/config/schema.ts`
- `packages/agm/src/config/load.ts`
- `packages/agm/src/config/paths.ts`
- `packages/agm/src/config/index.ts`

### CLI / 入口
- `packages/agm/src/index.ts`
- `packages/agm/src/cli/commands/send.ts`
- `packages/agm/src/cli/commands/reply.ts`
- `packages/agm/src/cli/commands/read.ts`
- `packages/agm/src/cli/commands/list.ts`
- `packages/agm/src/cli/commands/archive.ts`
- `packages/agm/src/cli/commands/bootstrap.ts`
- `packages/agm/src/cli/commands/doctor.ts`
- `packages/agm/src/cli/commands/log.ts`
- `packages/agm/src/cli/commands/config.ts`

### App / Runtime
- `packages/agm/src/app/send-message.ts`
- `packages/agm/src/app/reply-message.ts`
- `packages/agm/src/app/read-message.ts`
- `packages/agm/src/app/list-messages.ts`
- `packages/agm/src/app/archive-message.ts`
- `packages/agm/src/app/run-daemon.ts`
- `packages/agm/src/app/remote-mail-discovery.ts`

### State / Log / Activator / Git
- `packages/agm/src/activator/checkpoint-store.ts`
- `packages/agm/src/activator/index.ts`
- `packages/agm/src/log/events.ts`
- `packages/agm/src/git/repo.ts`
- `packages/agm/src/git/preflight.ts`
- `packages/agm/src/doctor/index.ts`
- `packages/agm/src/doctor/checks/config.ts`
- `packages/agm/src/doctor/checks/git.ts`
- `packages/agm/src/doctor/checks/runtime.ts`
- `packages/agm/src/doctor/checks/state.ts`
- `packages/agm/src/host-adapter/index.ts`

---

## 三、目标配置与路径模型

## 3.1 配置模型

顶层只认：

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
    activation:
      enabled: true
      activator: feishu-openclaw-agent
      dedupe_mode: filename
      feishu:
        open_id: ou_xxx
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

### 规则
- `profiles.<name>.self.id` 必须与 profile 语义一致
- `contacts` 只保留 `remote_repo_url`
- `notifications` / `runtime` / `activation` / `host_integration` 全部进 profile 命名空间
- 旧顶层 `self` / `contacts` / `runtime` / `notifications` / `host_integration` 全部移除

## 3.2 路径模型

### self repo
```text
~/.agm/profiles/<profile>/self
```

### contact cache
```text
~/.agm/profiles/<profile>/contacts/<contact>
```

### state
```text
~/.config/agm/state/<profile>/activation-state.json
~/.config/agm/state/<profile>/events.jsonl
~/.config/agm/state/<profile>/session-bindings.json
```

### 核心判断
- self repo 是 ownership state
- contact cache 是 disposable local cache
- 同机与跨机保持一致语义

---

## 四、代码改动清单

## 4.1 新增文件

### Config / Resolver
- Create: `packages/agm/src/config/profile.ts`
- Create: `packages/agm/src/config/profile-paths.ts`

### Contact cache
- Create: `packages/agm/src/git/contact-cache.ts`

### 测试（按仓库现有测试组织方式补充）
- Create/Modify: `packages/agm/src/config/__tests__/schema.test.ts`
- Create/Modify: `packages/agm/src/config/__tests__/profile.test.ts`
- Create/Modify: `packages/agm/src/config/__tests__/paths.test.ts`
- Create/Modify: `packages/agm/src/cli/__tests__/profile-argv.test.ts`
- Create/Modify: `packages/agm/src/app/__tests__/run-daemon-profile.test.ts`
- Create/Modify: `packages/agm/src/git/__tests__/contact-cache.test.ts`

## 4.2 修改文件

### Config / Paths
- Modify: `packages/agm/src/config/schema.ts`
- Modify: `packages/agm/src/config/load.ts`
- Modify: `packages/agm/src/config/paths.ts`
- Modify: `packages/agm/src/config/index.ts`

### CLI / Commands
- Modify: `packages/agm/src/index.ts`
- Modify: `packages/agm/src/cli/commands/send.ts`
- Modify: `packages/agm/src/cli/commands/reply.ts`
- Modify: `packages/agm/src/cli/commands/read.ts`
- Modify: `packages/agm/src/cli/commands/list.ts`
- Modify: `packages/agm/src/cli/commands/archive.ts`
- Modify: `packages/agm/src/cli/commands/bootstrap.ts`
- Modify: `packages/agm/src/cli/commands/doctor.ts`
- Modify: `packages/agm/src/cli/commands/log.ts`
- Modify: `packages/agm/src/cli/commands/config.ts`

### App / Runtime
- Modify: `packages/agm/src/app/send-message.ts`
- Modify: `packages/agm/src/app/reply-message.ts`
- Modify: `packages/agm/src/app/read-message.ts`
- Modify: `packages/agm/src/app/list-messages.ts`
- Modify: `packages/agm/src/app/archive-message.ts`
- Modify: `packages/agm/src/app/run-daemon.ts`
- Modify: `packages/agm/src/app/remote-mail-discovery.ts`

### State / Log / Activator / Doctor / Host Adapter
- Modify: `packages/agm/src/activator/checkpoint-store.ts`
- Modify: `packages/agm/src/activator/index.ts`
- Modify: `packages/agm/src/log/events.ts`
- Modify: `packages/agm/src/doctor/index.ts`
- Modify: `packages/agm/src/doctor/checks/config.ts`
- Modify: `packages/agm/src/doctor/checks/git.ts`
- Modify: `packages/agm/src/doctor/checks/runtime.ts`
- Modify: `packages/agm/src/doctor/checks/state.ts`
- Modify: `packages/agm/src/host-adapter/index.ts`

---

## 五、分阶段实施计划（最终版）

## Phase 1：Config Schema V3 + Profile Resolver

**目标：** 把 AGM 的配置语义切到 profile-first，并建立显式 profile resolver。

### 任务
- [ ] 在 `config/schema.ts` 定义新的 `ConfigSchema`，顶层只认 `profiles`
- [ ] 删除旧 `ConfigSchemaV2 / LegacyConfigSchemaV1 / LegacyConfigSchemaV0` 的正式支持
- [ ] 定义 `ProfileSchema`：`self / contacts / notifications / runtime / activation / host_integration`
- [ ] 在 `config/profile.ts` 新增 `resolveProfile(config, profileName)`
- [ ] 在 `config/profile.ts` 新增 `requireProfile(profileName?)`
- [ ] 在 `config/index.ts` 导出新的 resolver 与类型
- [ ] 补 profile schema / resolver 单测

### 关键约束
- `loadConfig()` 只解析新 schema
- 旧 schema 直接报错
- 不做 V2 fallback

### 验收
- profile config 解析成功
- profile 不存在时明确报错
- 旧单实例 config 无法通过加载

---

## Phase 2：Path Resolver + Profile State 目录重构

**目标：** 把 self repo、contact cache、events、checkpoint、session-bindings 全部 profile 化。

### 任务
- [ ] 在 `config/profile-paths.ts` 定义 profile 相关路径函数
- [ ] 派生 self repo 路径：`~/.agm/profiles/<profile>/self`
- [ ] 派生 contact cache 路径：`~/.agm/profiles/<profile>/contacts/<contact>`
- [ ] 派生 state 根目录：`~/.config/agm/state/<profile>/`
- [ ] 改造 `checkpoint-store.ts`，函数签名显式吃 `profile`
- [ ] 改造 `log/events.ts`，函数签名显式吃 `profile`
- [ ] 调整 `config/paths.ts` 与 `config/index.ts` 的导出
- [ ] 补 paths / state 单测

### 必须收死的 API 语义

#### checkpoint-store
```ts
hasActivated(profile: string, selfId: string, filename: string)
markActivated(profile: string, selfId: string, filename: string)
```

#### log/events
```ts
appendEvent(profile: string, event: EventRecord): void
parseEvents(profile: string, opts?): EventRecord[]
queryLastEvent(profile: string, type: EventType): EventRecord | null
```

### 验收
- `mt` / `hex` state 文件落到不同目录
- 同文件名邮件不会跨 profile 污染 checkpoint
- log 按 profile 分流

---

## Phase 3：CLI 全局 `--profile` 改造

**目标：** 所有命令必须显式提供 `--profile`，并把 profile 传到各层。

### 任务
- [ ] 改造 `packages/agm/src/index.ts` 的 argv 解析
- [ ] 除 `help` 外，所有命令都强制要求 `--profile`
- [ ] 给 `--profile` 提供 `-p` 简写
- [ ] 缺失 profile 时统一报错
- [ ] 各命令 handler 都接收 `profile`
- [ ] `daemon / doctor / log / bootstrap / config` 统一接入 profile
- [ ] 补 CLI 参数解析测试

### 错误语义
缺失 `--profile`：
```text
Missing required option: --profile
```

profile 不存在：
```text
Unknown profile: hex
Available profiles: mt, rk
```

### 验收
- `agm send ...` 不带 `--profile` 报错
- `agm --profile mt send ...` 成功解析
- `help` 不要求 `--profile`

---

## Phase 4：App 层与 Runtime Profile 化

**目标：** send/read/reply/list/archive/daemon 都基于当前选中的 profile 工作。

### 任务
- [ ] `send-message.ts` 改为显式接收 `profile + resolvedProfile`
- [ ] `reply-message.ts` 改为显式接收 `profile + resolvedProfile`
- [ ] `read-message.ts / list-messages.ts / archive-message.ts` 禁止走旧全局 self 假设
- [ ] `run-daemon.ts` 改为 `runDaemon({ profile, resolvedProfile, ... })`
- [ ] daemon 只 watch 当前 profile 的 self repo
- [ ] host integration / activation 只在当前 profile 范围内解析
- [ ] 补 app/runtime 相关测试

### 关键实现要求
- app 层不再通过“全局 self”做 repo lookup
- daemon 的 checkpoint / event log 必须显式带 `profile`
- `activator/index.ts` 与 `host-adapter/index.ts` 只能从当前 profile 读取配置

### 验收
- `mt` daemon 只处理 `mt` self repo
- `hex` daemon 只处理 `hex` self repo
- activation/log/checkpoint 都只写当前 profile

---

## Phase 5：Contact Cache Manager

**目标：** 把 contact cache 的 clone/fetch/update 行为收敛成独立模块。

### 任务
- [ ] 在 `git/contact-cache.ts` 实现 `ensureContactCache()`
- [ ] 在 `git/contact-cache.ts` 实现 `refreshContactCache()`
- [ ] 首次访问 contact 自动 clone 到派生路径
- [ ] 后续访问 contact 自动 fetch/pull
- [ ] origin mismatch 明确报错
- [ ] remote 不存在 / 不可达时返回明确错误
- [ ] send/read/list/reply 统一改走 contact cache manager
- [ ] 补 contact cache 单测

### 为什么放在 `git/` 边界
- 这是 git clone/fetch/update 的职责
- 不是 app orchestration
- 放在 `git/` 下比放在 `app/` 下更符合职责边界

### 验收
- 首次访问自动 clone
- 二次访问自动更新
- 不同 profile 对同一 contact 生成不同 cache 路径

---

## Phase 6：Daemon + Activator + Host Integration 收口

**目标：** daemon 与 activation path 真正完成 profile 隔离。

### 任务
- [ ] `run-daemon.ts` 中所有 event/checkpoint 调用都带 `profile`
- [ ] `activator/index.ts` 从当前 profile 解析 activation 配置
- [ ] `host-adapter/index.ts` 从当前 profile 解析 `host_integration`
- [ ] 确保 `--profile mt daemon` 与 `--profile hex daemon` 可并存
- [ ] 补 daemon / activator / host integration 集成测试

### 验收
- 两个 daemon 可同时运行
- 不会交叉读取/写入对方 state
- host integration 与 activator 只绑定当前 profile

---

## Phase 7：Doctor / Log 全面 Profile 化

**目标：** 所有观测/诊断视图都只看当前 profile。

### 任务
- [ ] `doctor/index.ts` 接受 `profile`
- [ ] `doctor/checks/config.ts` 检查当前 profile 配置完整性
- [ ] `doctor/checks/git.ts` 检查当前 profile self repo + contact cache
- [ ] `doctor/checks/state.ts` 检查当前 profile state 目录
- [ ] `doctor/checks/runtime.ts` 检查当前 profile runtime/event 状态
- [ ] `log.ts` 只读当前 profile 的 event log
- [ ] 补 doctor/log 测试

### 验收
- `agm --profile mt doctor` 只显示 mt 相关检查
- `agm --profile hex log` 只显示 hex 相关事件

---

## Phase 8：Bootstrap 收尾 + Docker 隔离 E2E

**目标：** 形成真正可用的新模型，并在隔离测试环境中完成 MT/Hex 双 profile 验证。

### 任务
- [ ] `bootstrap.ts` 改成 profile-aware
- [ ] `bootstrap` 写入 `profiles.<name>`，不覆盖其他 profile
- [ ] 初始化 self repo 到 `~/.agm/profiles/<profile>/self`
- [ ] 不再要求 `--self-local-repo-path`
- [ ] 在 Docker 隔离环境中跑 MT 单 profile E2E
- [ ] 在 Docker 隔离环境中跑 MT / Hex 双 profile E2E

### E2E 最少验证链路
1. `agm --profile mt send --to hex ...`
2. `agm --profile hex daemon --once` 检测新信
3. `agm --profile hex reply ...` 回给 MT
4. `mt` / `hex` 的 event log / checkpoint / state 完全隔离

### 测试纪律（必须遵守）
- **E2E 默认必须在 Docker 隔离环境中执行**
- **禁止使用本机常驻 HappyClaw / OpenClaw 实例作为完成验收依据**
- 本机常驻实例最多只可用于临时探索或人工调试，**不计入完成声明**
- 如果确实需要使用常驻实例侧验证，必须先停下相关实例，并等待贵平明确拍板后才能操作
- 未经贵平明确确认，不允许直接上手测本机常驻实例

### 关键判断
- **Phase 1/2 完成后可开始先跑 MT profile 的单元/集成验证**
- **Phase 6 完成后可开始准备 Docker 化双 profile E2E 环境**
- **Phase 8 完成且 Docker E2E 跑通后，才视为 profile 模型主链闭环**

---

## 六、测试计划（最终版）

## 6.1 Config / Resolver
- [ ] profile config 解析成功
- [ ] 缺失 `profiles` 失败
- [ ] profile 不存在失败
- [ ] 旧单实例 config 失败
- [ ] `contacts.repo_path` / `contacts.local_cache_path` 非法字段失败

## 6.2 Path / State
- [ ] `getSelfRepoPath(mt) !== getSelfRepoPath(hex)`
- [ ] `getContactCachePath(mt, hex) !== getSelfRepoPath(hex)`
- [ ] `getEventsPath(mt) !== getEventsPath(hex)`
- [ ] checkpoint/state 文件按 profile 隔离

## 6.3 CLI
- [ ] `agm send ...` 无 `--profile` 失败
- [ ] `agm --profile mt send ...` 成功解析
- [ ] `agm --profile hex daemon` 成功解析
- [ ] `help` 不要求 `--profile`

## 6.4 App / Runtime
- [ ] send 使用当前 profile 的 self repo
- [ ] read/list/archive 不会跨 profile 误读
- [ ] daemon 只 watch 当前 profile self repo
- [ ] activation/checkpoint/log 只写当前 profile state

## 6.5 Contact Cache
- [ ] 首次访问自动 clone
- [ ] 二次访问自动 fetch/pull
- [ ] remote mismatch 明确报错
- [ ] repo 不存在时错误清晰

## 6.6 E2E
- [ ] MT / Hex 双 profile 共存
- [ ] MT 发给 Hex 成功
- [ ] Hex daemon 检测并触发
- [ ] Hex 回复 MT 成功
- [ ] 两边的 event log / checkpoint 文件互不污染
- [ ] 以上 E2E 全部在 Docker 隔离环境中完成
- [ ] 未把本机常驻 HappyClaw / OpenClaw 实例作为验收依据

---

## 七、风险清单（最终版）

### 风险 1：旧 helper 大量默认“全局 self”
像 `getSelfId()`、`getSelfLocalRepoPath()`、`getAgentRepoPath()` 等 helper 已经把“全局只有一个 self”写进语义里。必须系统性替换，不能局部 patch。

### 风险 2：CLI 入口改造不彻底
如果有任何命令漏掉 `--profile` 约束，整个模型都会漏水。必须从 `packages/agm/src/index.ts` 的全局入口层统一卡死。

### 风险 3：state path 改了一半
如果 config 是 profile 化的，但 checkpoint / log / doctor 仍然用全局路径，就会形成“伪 profile”，这是不能接受的。

### 风险 4：contact cache 与 self repo 边界重新混淆
如果后续实现里继续允许某些命令直接走 contact 本体 repo 路径，就会把旧模型偷偷带回来。必须从 schema 和 API 层彻底删掉这种语义。

### 风险 5：bootstrap 容易退回单实例思维
当前 bootstrap 天然偏“初始化一套 self”。改造时必须改成“向 `profiles` 写入/更新一个 profile”，而不是覆盖整个 config。

### 风险 6：范围漂移到迁移兼容
本轮最容易被错误拉宽的范围是“顺手兼容旧模型”或“补迁移脚本”。这会显著提高复杂度，并削弱新模型边界。**本轮不纳入。**

### 风险 7：把本机常驻实例误当成正式验收环境
如果直接使用本机正在运行的 HappyClaw / OpenClaw 做 E2E，很容易把历史状态、真实路由、手工配置和运行时噪音误判成实现正确。**默认禁止**把常驻实例作为完成验收依据；如确有必要，必须先停实例并等待贵平明确判断。

---

## 八、推荐执行顺序（最终）

如果只允许给一个执行顺序，采用如下版本：

1. **先做 Config Schema V3 + Profile Resolver**
2. **再做 Path Resolver + Profile State**
3. **再做 CLI 全局 `--profile` 改造**
4. **然后改 App / Runtime**
5. **再抽 Contact Cache Manager**
6. **收口 Daemon / Activator / Host Integration**
7. **收口 Doctor / Log**
8. **最后做 Bootstrap + 双 Profile E2E**

### 顺序理由
- config/path 不先定死，后面所有模块都会返工
- CLI 不先全局卡死，runtime 改造极易漏入口
- contact cache 是核心边界，但可在 runtime 主链跑通后再独立抽象
- bootstrap 必须后置，否则容易沿用旧单实例思维做错

---

## 九、最终结论

这份 final 版融合了两部分优点：

- **Hex 版**：8 个 phase 拆分清楚，blocker 与验证节点表达明确
- **MT 版**：真实代码路径更准确，范围收口更硬，边界与 API 语义更明确

最终 baseline 应以这份为准：

> **采用 Hex 的 8 phase 实施拆分，但按 MT 版修正文件路径、模块边界与范围收口：不做旧配置迁移，不保留兼容模式，contact cache 归入 git 边界，checkpoint/log API 必须显式 profile 化。**
