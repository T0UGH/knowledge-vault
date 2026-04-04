# AGM Profile 模型 Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 把 AGM 从当前单实例/环境变量切换模型，升级为强制显式 `--profile` 的多 profile mailbox 模型，并让 MT / Hex 作为两个独立 owner profile 共存。

**Architecture:** 以 `profile` 作为 AGM 顶层执行上下文，重构 config schema、path resolver、state/log/checkpoint 目录、CLI 参数解析与 daemon/runtime 作用域。self repo、contact cache、runtime state 全部按 profile 隔离；contacts 只认 `remote_repo_url`，本地 cache 路径由 AGM 自动派生。

**Tech Stack:** TypeScript, Node.js, YAML config, Git-based mailbox repos, AGM CLI/runtime.

---

## 一、范围和拍板

### 明确目标
这次不是在旧模型上“补一个 profile 参数”，而是**直接切到新模型**：

- 所有命令必须显式 `--profile`
- `profiles.<name>` 成为唯一配置入口
- self repo / contact cache / activation-state / events / session-bindings / daemon 全部按 profile 隔离
- 不兼容旧 `self + contacts` 单实例 schema
- 不再把 `AGM_CONFIG_DIR` 作为主工作流；它最多只保留成 debug/override 能力

### 本轮不做的事
- 不做旧配置自动迁移
- 不做 default profile
- 不做 cross-profile 聚合 log/doctor
- 不做“直接使用 contact self repo 本体”的优化
- 不做 UI，只改 CLI/runtime

---

## 二、代码改动总览

### 核心会改的文件

#### Config / 路径
- Modify: `packages/agm/src/config/schema.ts`
- Modify: `packages/agm/src/config/load.ts`
- Modify: `packages/agm/src/config/paths.ts`
- Modify: `packages/agm/src/config/index.ts`

#### CLI 入口
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

#### App / Runtime
- Modify: `packages/agm/src/app/send-message.ts`
- Modify: `packages/agm/src/app/reply-message.ts`
- Modify: `packages/agm/src/app/read-message.ts`
- Modify: `packages/agm/src/app/list-messages.ts`
- Modify: `packages/agm/src/app/archive-message.ts`
- Modify: `packages/agm/src/app/run-daemon.ts`
- Modify: `packages/agm/src/app/remote-mail-discovery.ts`

#### State / Log / Activator
- Modify: `packages/agm/src/activator/checkpoint-store.ts`
- Modify: `packages/agm/src/log/events.ts`
- Modify: `packages/agm/src/doctor/checks/config.ts`
- Modify: `packages/agm/src/doctor/checks/git.ts`
- Modify: `packages/agm/src/doctor/checks/runtime.ts`
- Modify: `packages/agm/src/doctor/checks/state.ts`

#### 建议新增的文件
- Create: `packages/agm/src/config/profile.ts`
- Create: `packages/agm/src/config/profile-paths.ts`
- Create: `packages/agm/src/git/contact-cache.ts`

#### 测试文件
- Create/Modify: `packages/agm/src/config/__tests__/schema.test.ts`
- Create/Modify: `packages/agm/src/config/__tests__/profile.test.ts`
- Create/Modify: `packages/agm/src/config/__tests__/paths.test.ts`
- Create/Modify: `packages/agm/src/cli/__tests__/profile-argv.test.ts`
- Create/Modify: `packages/agm/src/app/__tests__/run-daemon-profile.test.ts`
- Create/Modify: `packages/agm/src/git/__tests__/contact-cache.test.ts`

---

## 三、分阶段实施计划

## Phase 1：Profile config schema 与 resolver 收口

**目标：** 先把 AGM 的“配置语义”切到 profile-first，不再依赖旧单实例 schema。

### 需要达成的结果
- `config.yaml` 顶层只认 `profiles`
- 每次执行必须先选中一个 profile
- 从 profile 中解析：
  - self
  - contacts
  - runtime
  - notifications
  - activation / host_integration

### 设计建议
新增一个显式的 resolver 层，不要把 profile 逻辑散落在各命令里。

#### `config/schema.ts`
把当前：
- `ConfigSchemaV2`
- `LegacyConfigSchemaV1`
- `LegacyConfigSchemaV0`

全部收掉，直接定义新 schema：

```ts
const ProfileSelfSchema = z.object({
  id: z.string().min(1),
  remote_repo_url: z.string().min(1),
});

const ProfileContactSchema = z.object({
  remote_repo_url: z.string().min(1),
});

const ProfileSchema = z.object({
  self: ProfileSelfSchema,
  contacts: z.record(z.string(), ProfileContactSchema).default({}),
  notifications: NotificationsConfigSchema.optional().default({}),
  runtime: RuntimeConfigSchema.optional().default({}),
  activation: ActivationConfigSchema.optional(),
  host_integration: HostIntegrationConfigSchema.optional(),
});

export const ConfigSchema = z.object({
  profiles: z.record(z.string(), ProfileSchema).refine(
    (profiles) => Object.keys(profiles).length > 0,
    'at least one profile is required',
  ),
});
```

#### `config/profile.ts`
新增 profile resolver：

```ts
export interface ResolvedProfileConfig {
  profileName: string;
  self: { id: string; remote_repo_url: string };
  contacts: Record<string, { remote_repo_url: string }>;
  notifications: NotificationsConfig;
  runtime: RuntimeConfig;
  activation?: ActivationConfig;
  host_integration?: HostIntegrationConfig;
}

export function resolveProfile(config: Config, profileName: string): ResolvedProfileConfig
export function requireProfile(profileName?: string): string
```

#### `config/index.ts`
导出：
- `resolveProfile`
- `requireProfile`
- `type ResolvedProfileConfig`

### 测试
- profile schema 正确解析
- profile 不存在时报错
- 空 `profiles` 报错
- `contacts.repo_path` / legacy 字段不再被接受

### 提交粒度
- `feat: replace legacy AGM config with profile-first schema`

---

## Phase 2：Path resolver 与 profile state 目录重构

**目标：** 把 repo 路径、state 路径、event/checkpoint 路径全部 profile 化。

### 需要达成的结果
- self repo 路径统一派生：`~/.agm/profiles/<profile>/self`
- contact cache 路径统一派生：`~/.agm/profiles/<profile>/contacts/<contact>`
- state 路径统一派生：`~/.config/agm/state/<profile>/...`

### 文件设计

#### `config/paths.ts`
保留全局 config 文件路径概念，例如：
- `getConfigPath()`

但去掉“所有 state 都直接挂在 config dir 下”的旧模型。

#### 新增 `config/profile-paths.ts`
建议集中定义：

```ts
export function getAgmRootDir(): string
export function getProfileRootDir(profile: string): string
export function getSelfRepoPath(profile: string): string
export function getContactCachePath(profile: string, contact: string): string

export function getStateRootDir(profile: string): string
export function getActivationStatePath(profile: string): string
export function getEventsPath(profile: string): string
export function getSessionBindingsPath(profile: string): string
```

### 状态相关改造

#### `activator/checkpoint-store.ts`
当前签名：
```ts
hasActivated(selfId, filename)
markActivated(selfId, filename)
```

需要升级为显式 profile 作用域：

```ts
hasActivated(profile: string, selfId: string, filename: string)
markActivated(profile: string, selfId: string, filename: string)
```

并让底层文件路径指向：
- `state/<profile>/activation-state.json`

#### `log/events.ts`
当前 `appendEvent()` 直接写全局 `events.jsonl`。  
需要改为：

```ts
appendEvent(profile: string, event: EventRecord): void
parseEvents(profile: string, opts?): EventRecord[]
queryLastEvent(profile: string, type: EventType): EventRecord | null
```

### 测试
- 不同 profile 的路径互不相同
- `mt` / `hex` 的 state 文件不会写到同一目录
- 同文件名邮件在不同 profile 下 checkpoint key 不冲突

### 提交粒度
- `refactor: scope AGM paths and state by profile`

---

## Phase 3：CLI 全局 `--profile` 改造

**目标：** 所有命令都必须显式传 `--profile`，并把 profile 一路传进 app/runtime 层。

### 需要达成的结果
- `agm --profile mt send ...` 生效
- `agm send ...` 直接报错
- `daemon / doctor / log / bootstrap / config` 也统一走 `--profile`

### 文件设计

#### `index.ts`
当前 CLI parser 允许随意解析 subcommand 和 flags。  
这里要改成：

1. 在全局 argv 层读取 `--profile`
2. 所有非 `help` 命令都必须带 profile
3. handler 统一收到 `profile`

建议新增：

```ts
export interface ParsedCommand {
  subcommand: string;
  profile: string;
  argv: Record<string, unknown>;
}
```

### 各命令要求

#### send/reply/read/list/archive
- 保留业务参数
- 增加 `profile`
- app 层不再自行猜 config 作用域

#### daemon
当前直接 `loadConfig()`。  
要改成：
- `loadConfig()`
- `resolveProfile(config, profile)`
- `runDaemon({ profile, resolvedProfile })`

#### doctor / log / config
都必须只看当前 profile 作用域。

#### bootstrap
也必须强制 `--profile`，但语义要变：
- bootstrap 某个 profile 的 self repo 初始化
- 写入 `profiles.<profile>` 节点
- 不再覆盖整个 config 为单实例结构

### 错误行为
缺失 `--profile`：
```text
Missing required option: --profile
```

profile 不存在：
```text
Unknown profile: hex
Available profiles: mt, rk
```

### 测试
- argv 解析测试
- 未带 profile 时失败
- 带 profile 时各子命令能收到 profile
- help 不强制 profile

### 提交粒度
- `feat: require explicit --profile for all AGM commands`

---

## Phase 4：App 层与 daemon/runtime 改造

**目标：** send/read/reply/list/archive/daemon 都真正基于 resolved profile 工作。

### 需要达成的结果
- app 层不再读取“全局 self”
- daemon 只 watch 当前 profile 的 self repo
- host integration / activation 只在当前 profile 范围内生效

### 文件设计

#### `send-message.ts`
当前会从 config 里读 self 和 contacts。  
改为显式吃：

```ts
sendMessage({
  profile: string,
  resolvedProfile: ResolvedProfileConfig,
  ...
})
```

self repo 路径由：
- `getSelfRepoPath(profile)`

contact cache 路径由：
- `getContactCachePath(profile, to)`

如果 contact cache 不存在：
- 自动 clone remote

#### `reply-message.ts`
同理，不能再走旧的 agent path lookup。

#### `read-message.ts` / `list-messages.ts` / `archive-message.ts`
只允许读：
- 当前 profile 的 self repo
- 当前 profile 自己维护的 contact cache
- 不允许直接引用别的 profile 的 self repo 路径

#### `run-daemon.ts`
这是主战场之一。

当前逻辑里：
- `getSelfId()`
- `getSelfLocalRepoPath()`
- 全局 events / checkpoint
- hostAdapter / activator 直接从全局 config 读

都要改为：
- 基于 `ResolvedProfileConfig`
- `self.id` 来自当前 profile
- self repo 路径来自 `getSelfRepoPath(profile)`
- checkpoint 写到 `state/<profile>`
- events 写到 `state/<profile>/events.jsonl`
- host integration 解析当前 profile 的配置

建议新签名：

```ts
runDaemon({
  profile: string,
  resolvedProfile: ResolvedProfileConfig,
  ...
})
```

### 测试
- daemon 只处理当前 profile self repo 的 inbox
- `mt` daemon 不会看 `hex` self repo
- activation sent/failed 只写当前 profile event log
- checkpoint 只写当前 profile state

### 提交粒度
- `refactor: scope AGM app runtime to selected profile`

---

## Phase 5：Contact cache 管理器与 remote peer 语义收口

**目标：** 把 contact cache 的 clone/fetch 行为收成清晰模块，而不是散在 send/read/list 各命令里。

### 新增 `git/contact-cache.ts`
建议统一提供：

```ts
export async function ensureContactCache(opts: {
  profile: string;
  contact: string;
  remoteRepoUrl: string;
}): Promise<{ path: string; created: boolean }>

export async function updateContactCache(opts: {
  profile: string;
  contact: string;
}): Promise<void>
```

### 行为要求
- 首次访问 contact：
  - 自动 clone 到派生路径
- 后续访问：
  - 自动 fetch / pull
- 如果 remote 不存在：
  - 返回明确错误
- 如果本地 cache 已存在但 origin 不匹配：
  - 返回明确错误，禁止静默覆盖

### 为什么单独抽模块
因为这是新的核心边界：

- contact 是 remote peer
- cache 是当前 profile 的局部实现细节
- 不能让 send/read/list/reply 各自复制一套 git 行为

### 测试
- 首次访问自动 clone
- 二次访问自动更新
- remote mismatch 报错
- 不同 profile 对同一 contact 生成不同 cache 路径

### 提交粒度
- `feat: add profile-scoped contact cache manager`

---

## Phase 6：bootstrap / doctor / log 收尾 + E2E

**目标：** 把产品面收干净，形成真正可用的新模型。

### bootstrap
需要改成 profile-aware：

```bash
agm --profile mt bootstrap --self-id mt --self-remote-repo-url ...
```

行为：
- 初始化 `profiles.mt.self`
- clone self repo 到 `~/.agm/profiles/mt/self`
- 不覆盖别的 profile
- 可多次为不同 profile 追加初始化

### doctor
检查当前 profile：
- config 中 profile 是否存在
- self repo 是否存在
- contact cache 是否健康
- state 文件是否可解析
- runtime 最近事件是否健康

### log
只显示当前 profile 的事件。

### E2E 验证目标
至少跑通这 4 条：

1. `agm --profile mt send --to hex ...`
2. `agm --profile hex daemon --once` 能检测 Hex self inbox 新信
3. `agm --profile hex reply ...` 能回给 MT
4. `mt` / `hex` 两套 state/log/checkpoint 完全隔离

### 提交粒度
- `feat: complete AGM profile-mode bootstrap doctor and e2e coverage`

---

## 四、测试计划

## 4.1 Config / resolver
- profile config 解析成功
- 缺失 `profiles` 失败
- profile 不存在失败
- legacy config 失败
- `contacts.repo_path` 非法字段失败

## 4.2 Path / state
- `getSelfRepoPath(mt) !== getSelfRepoPath(hex)`
- `getContactCachePath(mt, hex) !== getSelfRepoPath(hex)`
- `getEventsPath(mt) !== getEventsPath(hex)`
- checkpoint/state 文件按 profile 隔离

## 4.3 CLI
- `agm send ...` 无 `--profile` 失败
- `agm --profile mt send ...` 成功解析
- `agm --profile hex daemon` 成功解析
- `help` 不要求 `--profile`

## 4.4 App / runtime
- send 使用当前 profile 的 self repo
- read/list/archive 不会跨 profile 误读
- daemon 只 watch 当前 profile self repo
- activation/checkpoint/log 只写当前 profile state

## 4.5 Contact cache
- 首次访问自动 clone
- 二次访问自动 fetch/pull
- remote mismatch 明确报错
- repo 不存在时错误清晰

## 4.6 E2E
- MT / Hex 双 profile 共存
- MT 发给 Hex 成功
- Hex daemon 检测并触发
- Hex 回复 MT 成功
- 两边的 event log / checkpoint 文件互不污染

---

## 五、风险与 blocker

### 风险 1：旧代码大量默认“全局 self”
这是主风险。  
像 `getSelfId()`、`getSelfLocalRepoPath()` 这种 helper 已经把“全局只有一个 self”写进去了，必须系统性替换，不能局部 patch。

### 风险 2：CLI 入口改造不彻底
如果某几个命令漏掉 `--profile`，整个模型就会漏水。  
必须从 `index.ts` 全局入口层统一卡住。

### 风险 3：state path 改了一半
如果 config 是 profile 化的，但 checkpoint / log / doctor 仍然用全局路径，就会形成“伪 profile”，这是不能接受的。

### 风险 4：contact cache 和 self repo 边界混淆
如果继续允许某些命令直接走 contact repo_path，就会重新把旧模型带回来。  
必须从 schema 层直接删掉这种语义。

### 风险 5：bootstrap 容易退回单实例思维
当前 bootstrap 是“初始化一套 self”。  
改造时要避免继续覆盖整个 config，而是变成“向 profiles 里追加/更新一个 profile”。

---

## 六、推荐执行顺序

如果只给一个顺序，我建议：

1. **先做 config schema + profile resolver**
2. **再做 path/state resolver**
3. **再做 CLI 全局 `--profile` 改造**
4. **然后改 app/runtime/daemon**
5. **再抽 contact cache manager**
6. **最后收 bootstrap/doctor/log + 跑双 profile E2E**

原因很简单：
- 不先把 config/path 定死，后面所有改动都会返工
- 不先把 CLI 全局 `--profile` 卡死，runtime 改造很容易漏入口
- contact cache 是核心边界，但可以在 runtime 主链跑通后再抽模块收尾

---

## 七、我对实施复杂度的判断

这不是一行小改，属于**中等规模结构重构**。  
但它的边界是清晰的，主战场非常集中：

- `config/*`
- `index.ts`
- `run-daemon.ts`
- `checkpoint-store.ts`
- `log/events.ts`
- `bootstrap/doctor/log`
- 新增 `contact-cache.ts`

换句话说：**改动面不小，但不是发散型 chaos。**

---

## 八、最终建议

正式按这条路线推进：

> AGM 直接切到 profile-first 新模型，不兼容旧 schema；所有命令显式 `--profile`；self/contact/state/runtime 全部按 profile 隔离；contact 只认 remote，本地副本只作为 profile-scoped cache。
