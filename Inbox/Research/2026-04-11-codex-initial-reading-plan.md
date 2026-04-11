# 2026-04-11 Codex 初步阅读计划

## 结论

Codex 这仓库不是“一个 CLI 项目”，而是一个 **Rust 核心 runtime 平台**，外面挂着：

- `codex-cli/`：npm 分发壳
- `sdk/`：Python / TypeScript SDK
- `docs/`：用户与实现文档
- `codex-rs/`：真正主战场

而 `codex-rs/` 自身又是一个大型 Rust workspace。

所以阅读不能按目录顺序平铺扫，而应该按 **系统分层** 来读。

## 总体阅读策略

不建议按 crate 一个一个读。
更合理的方式是沿 4 条主线并行收材料：

- **入口主线**
- **状态主线**
- **能力接入主线**
- **执行边界主线**

这样后面产出的 Phase 1 物料，不会变成“crate 说明书”，而会更像真正有价值的源码拆解。

## 建议的 5 阶段阅读框架

### Phase 0：建立总图，不碰细节

目标：先回答 **“Codex 到底是什么系统”**。

#### 推荐先看

- `README.md`
- `docs/install.md`
- `codex-cli/bin/codex.js`
- `codex-rs/cli/src/main.rs`
- `codex-rs/core/src/lib.rs`
- `codex-rs/tui/src/lib.rs`
- `codex-rs/config/src/lib.rs`
- `codex-rs/state/src/lib.rs`

#### 这一阶段要收出的结论

1. npm 包只是分发壳，主体是 Rust binary
2. 默认入口是 interactive TUI，不只是 exec
3. `cli / tui / core / config / state` 各自站什么位置
4. Codex 是 runtime，不只是命令行包装器

#### 推荐产物

- `仓库总貌与分层判断`
- `代码路径索引-入口与分发`
- `代码路径索引-core-tui-config-state`

---

### Phase 1：入口与运行主线

目标：回答 **“一条请求怎么跑起来”**。

#### 推荐先看

- `codex-rs/cli/src/main.rs`
- `codex-rs/cli/src/lib.rs`
- `codex-rs/tui/src/lib.rs`
- `docs/exec.md`
- `docs/slash_commands.md`
- `docs/tui-request-user-input.md`

#### 重点问题

1. `codex` 命令怎么分流到 TUI / exec / review / mcp / app-server
2. TUI 是直接调用 core，还是经 app-server 中转
3. interactive 模式和 `exec` 模式主线差别在哪
4. session / thread / turn 这些抽象在哪一层出现

#### 推荐产物

- `cli总入口与命令分流`
- `interactive-tui 主链草图`
- `exec 非交互主链草图`

---

### Phase 2：配置、状态、恢复

目标：回答 **“Codex 怎么记住自己、恢复自己、约束自己”**。

#### 推荐先看

- `codex-rs/config/`
- `codex-rs/state/`
- `docs/config.md`
- `docs/example-config.md`
- `docs/authentication.md`

#### 重点问题

1. `~/.codex/config.toml` 怎么建模
2. config layer / overrides / MCP config / skills config 怎么组织
3. state 为什么单独成 crate
4. SQLite state DB 存什么，不存什么
5. thread / session 恢复和 rollout/session metadata 的关系是什么

#### 推荐产物

- `config 模型与层叠加载`
- `state crate 在系统里的职责`
- `session / thread / rollout 恢复链`

---

### Phase 3：能力面接入

目标：回答 **“Codex 的外部能力怎么接进 runtime”**。

#### 推荐先看

- `codex-rs/codex-mcp/`
- `codex-rs/mcp-server/`
- `codex-rs/rmcp-client/`
- `codex-rs/skills/`
- `codex-rs/hooks/`
- `docs/skills.md`
- `docs/agents_md.md`

#### 重点问题

1. MCP 在 Codex 里是“工具协议”还是“能力总线”
2. skills 在系统里处于 prompt 层、配置层还是 runtime 层
3. hooks 是 UI convenience，还是 runtime 编排入口
4. `AGENTS.md` 在 Codex 里到底起什么作用

#### 推荐产物

- `MCP 接入面总览`
- `skills 在 Codex 里的位置`
- `hooks / AGENTS.md 的角色判断`

---

### Phase 4：执行边界与安全模型

目标：回答 **“Codex 为什么不是裸奔 agent”**。

#### 推荐先看

- `codex-rs/sandboxing/`
- `codex-rs/linux-sandbox/`
- `codex-rs/windows-sandbox-rs/`
- `codex-rs/process-hardening/`
- `codex-rs/execpolicy/`
- `docs/sandbox.md`
- `docs/execpolicy.md`

#### 重点问题

1. sandboxing 总抽象和平台实现怎么分层
2. Linux / macOS / Windows 的执行边界差异是什么
3. execpolicy 和 sandbox 是互补还是重叠
4. approval / network / cwd / write scope 的边界如何表达

#### 推荐产物

- `sandbox 总体设计`
- `execpolicy 与 sandbox 的关系`
- `Codex 执行边界模型`

---

### Phase 5：扩展形态与嵌入式用法

目标：回答 **“Codex 如何被别的系统调用”**。

#### 推荐先看

- `sdk/typescript/README.md`
- `sdk/python/README.md`
- `codex-rs/app-server/`
- `codex-rs/app-server-client/`
- `codex-rs/app-server-protocol/`
- `codex-rs/responses-api-proxy/`

#### 重点问题

1. SDK 为什么是 spawn CLI + JSONL event，而不是直接调库
2. app-server 在体系里是给 UI 服务，还是给 embedding 服务
3. Codex 的“可嵌入性”边界在哪
4. CLI、app-server、SDK 三者是什么关系

#### 推荐产物

- `SDK 为什么不直接链接 core`
- `app-server 在 Codex 里的角色`
- `Codex 的嵌入式架构`

## 第一批建议立刻阅读的文件

建议先读下面这批：

1. `README.md`
2. `codex-cli/bin/codex.js`
3. `codex-rs/cli/src/main.rs`
4. `codex-rs/core/src/lib.rs`
5. `codex-rs/tui/src/lib.rs`
6. `codex-rs/config/src/lib.rs`
7. `codex-rs/state/src/lib.rs`

这批读完后，先形成三篇最小骨架：

- `仓库总貌与分层判断`
- `为什么 npm 只是分发壳`
- `Codex CLI 总入口与命令分流`

## 当前最值得优先回答的 8 个问题

1. Codex 的真正主体在哪层？
2. TUI 和 core 的边界是什么？
3. `exec` 和 interactive 是不是同一套 runtime？
4. state 为什么单独成 crate？
5. config 的层叠模型怎么组织？
6. MCP 在系统里是外围能力还是核心能力？
7. sandbox / execpolicy / approval 三者怎么分工？
8. SDK 为什么选择 spawn CLI 而不是直接暴露库 API？

## 当前拍板

当前最合理的阅读方式是：

> **先建立 Codex 的系统总图，再沿入口、状态、能力接入、执行边界 4 条主线并行拆。**

不应该一上来就把所有 crate 平铺扫一遍。

一句话收口：

> 先抓骨架，再拆细节；先做结构化理解，再做局部深入。
