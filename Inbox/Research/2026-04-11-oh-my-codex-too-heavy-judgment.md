# 2026-04-11 oh-my-codex 太重了：阶段判断

## 结论

当前判断很明确：`oh-my-codex` **太重了**。

问题不是它做得差，而是它已经从“增强 Codex”滑到了“重造一层 agent runtime”。

对贵平当前阶段，更值得借鉴的是它背后的抽象，而不是直接接它整套实现。

## 为什么说它太重

### 1. 它不是工具增强，而是运行时接管

它不只是补几个命令，而是在接管：

- `.codex/config.toml`
- `.codex/hooks.json`
- `AGENTS.md`
- `.omx/` 状态目录
- tmux runtime
- worker inbox / mailbox / dispatch
- team phase / monitor snapshot / shutdown protocol

这已经不是“工具增强”，而是一整套有主张的运行系统。

### 2. 系统复杂度已经超过多数日常任务需要

它的 team mode 当然很完整：

- leader / worker 分工
- task state
- mailbox / dispatch
- tmux session 管理
- phase 状态机
- worktree 隔离
- monitor snapshot
- shutdown gate

但现实问题是：

> 日常大部分 Codex 协作，不需要这么厚的系统。

如果任务规模还没大到必须引入一整层状态机，这套东西就会先带来维护成本，而不是净收益。

### 3. 真正难的地方会从“做任务”转成“维护 runtime”

一旦系统变重，排查问题时，关注点会从：

- 需求是否收清
- 实现是否正确
- 验收是否通过

变成：

- tmux pane 为什么失联
- state 文件有没有脏
- dispatch request 为什么没 delivered
- monitor snapshot 为什么没刷新
- worktree 为什么冲突

这类成本对当前阶段来说，不划算。

## 当前阶段更适合贵平的方向

### 1. 要薄编排层，不要厚 runtime

更合适的方向不是引入一个“AI 团队操作系统”，而是只补真正高杠杆的薄层：

- 明确分层
- 明确状态
- 明确 leader / worker 协议
- 明确验收归口

### 2. tmux 只做承载，不做操作系统

tmux 很适合拿来承载长期会话和并发 session。

但不需要把：

- inbox
- mailbox
- dispatch
- HUD
- runtime hooks
- 监控系统

全部绑到 tmux 上。

更合理的做法是：

> tmux 负责承载会话；流程控制和状态约定尽量保持轻。

### 3. 多 agent 的重点不是“多开几个 agent”，而是协议清楚

真正要保留的，不是 OMX 那套完整实现，而是这些原则：

- leader 负责定义任务与收口
- worker 负责 bounded execution
- 任务状态要明确
- blocker 要显式上浮
- 验收层必须独立

### 4. 验收层依然是最高杠杆

这轮再次确认：

> 对贵平来说，真正最该强化的不是更复杂的 orchestrator，而是执行层和验收层的明确分离。

也就是说：

- worker 可以多
- 流程可以轻
- 但 verification 不能弱

## 推荐保留的轻量骨架

后续如果要收自己的 Codex / 多 agent 工作流，更建议只保留这 5 个点：

1. **任务分层**：clarify → plan → execute → verify
2. **轻量状态文件**：目标、当前状态、下一步、验收标准
3. **轻量任务状态**：`pending / in_progress / blocked / done`
4. **轻量 leader-worker 协议**：谁定义、谁执行、谁验收
5. **显式验收层**：不把完成判断混进执行线程里

## 当前拍板

- `oh-my-codex`：**值得研究，不值得直接用**
- 当前更适合贵平的方向：**薄编排层 + 强验收层**
- 不建议现在引入一整套重 runtime

一句话收口：

> 你现在需要的是“更轻的协作骨架”，不是“更重的 agent 操作系统”。
