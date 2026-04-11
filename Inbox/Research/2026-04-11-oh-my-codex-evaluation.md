# 2026-04-11 oh-my-codex 评估

## 结论

`oh-my-codex` 不是 Codex 的轻量增强，而是一层 **重度工作流/编排系统**。
对已经大量使用 Codex、并且开始重视多 agent、状态沉淀、验收纪律的人来说，**值得研究**；但 **不建议直接接管主环境**。

## 这项目本质上是什么

它给 OpenAI Codex CLI 外面套了一层统一工作流，核心是几套固定模式：

- `$deep-interview`：先澄清需求和边界
- `$ralplan`：产出并批准计划
- `$team`：tmux 多 worker 并行执行
- `$ralph`：持续推进直到完成并验证

一句话：它想把「让 Codex 写代码」升级成「让 Codex 按工程流程做事」。

## 它解决的问题

1. Codex 原生会话太轻，缺少稳定流程
2. 多 pane / 多 worker / 长任务缺少统一状态管理
3. 缺 durable artifacts，导致 handoff、恢复、复盘成本高

它的主要做法是把状态、计划、日志、memory 收到 `.omx/` 下面。

## 工程观察

### 优点

- 方法论清晰，不是单纯堆命令
- 对长任务、并行任务、验证闭环有明显强化
- 有 tmux runtime、HUD、native hooks、MCP-CLI parity，工程投入不小
- 文档和 release notes 比较完整
- 本地验证：`npm ci` 成功，`npm run build` 成功

### 风险 / 代价

- **侵入性强**：`omx setup` 会改 `.codex/config.toml`、`.codex/hooks.json`、repo 的 `AGENTS.md`，并引入 `.omx/`
- 心智负担不低：要接受它自己的工作流和术语体系
- 更像一套 opinionated runtime，而不是一个随手装的插件
- 依赖 tmux / hooks / MCP / 本地 CLI 环境，越强大越容易带来兼容和维护成本

## 当前成熟度判断

- GitHub stars：约 **20k+**
- 仓库活跃，最近仍有持续提交
- release notes 显示作者有持续做稳定性修补和测试补强
- 但不是“极致稳态成品”，更像快速演进中的强工程项目

## 对贵平的适配判断

### 适合的点

- 已大量使用 Codex
- 已开始重视流程、验收、handoff、多 agent 协作
- 不满足于“一次性生成代码”，而是想把 AI 开发流程制度化

### 不适合的点

- 如果已经有顺手的 tmux + 自定义规范 + 记忆体系
- 如果不希望工具主动改本地 hooks / config / AGENTS
- 如果当前更想要低侵入、低维护的增强，而不是一整套 runtime

## 建议的试用方式

不要直接装进主工作仓。

更合理的顺序：

1. 先在隔离测试仓跑 `omx setup`
2. 只验证 3 件事：
   - `$deep-interview / $ralplan` 是否真的提升需求收口质量
   - `$team` 的 tmux 并行是否比现有方式更顺
   - `.omx/` 的状态沉淀是否真的降低恢复成本
3. 如果这 3 件都成立，再考虑是否纳入主工作流

## 当前拍板

- **值得看**
- **值得试**
- **不建议直接全量接管主环境**

更准确地说：它对已经进入「AI 工程化」阶段的人有价值，但前提是你愿意接受一套更重、更有主张的运行时。
