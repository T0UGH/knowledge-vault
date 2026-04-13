# 2026-04-14 Hermes cron 升级到 daily-report-master-agent + profile 机制结论

- 时间：2026-04-14 00:xx Asia/Shanghai
- 相关仓库：
  - `~/workspace/daily-report-master-agent`
  - `~/.hermes/hermes-agent`
- 相关提交：
  - `654af5a` — `fix: make daily report output faithful to source`
  - `c7f5c90` — `improve: expand daily report detail richness`

## 背景

这轮先把 `daily-report-master-agent` 的 reader-facing 日报输出从“摘要腔/太短”往“忠实原文、中文整理、保留具体更新点”连续改了三轮；随后把 Hermes 里仍然指向旧 `daily-lane` prompt 链路的 cron job 原地升级为新的主链路，并补查了 Hermes 的 profile / 多实例 / 跨 profile 通信边界。

## 结论 1：当前日报主链路已经从老 `daily-lane` cron 切到新系统

Hermes 当前唯一活跃的日报 cron job 原本是：

- `job_id`: `1f7ef880ea07`
- `name`: `daily-lane-0830`
- `schedule`: `30 8 * * *`
- prompt 仍引用老文件：
  - `~/workspace/daily-lane/docs/prompts/orchestrator-v0.2.md`
  - `~/workspace/daily-lane/docs/prompts/editor-v0.3.md`
  - `~/workspace/daily-lane/docs/prompts/humanizer-v0.1.md`

已原地升级为：

- `job_id`: `1f7ef880ea07`
- `name`: `daily-report-master-0600`
- `schedule`: `0 6 * * *`
- `next_run_at`: `2026-04-14T06:00:00+08:00`
- `deliver`: `feishu`
- 新 prompt 明确要求：
  - 使用 `~/workspace/daily-report-master-agent` 作为当前权威逻辑
  - 读取 `~/.daily-lane-data/signals`
  - 中文优先
  - 忠实原文
  - 尽量保留具体更新点，不再走老 `daily-lane` prompt 链路

## 结论 2：`daily-report-master-agent` 这轮已经完成的质量升级

### 第一层：不再默认吃摘要腔

已把默认渲染优先级改成：

- 标题：`title > editor_headline > fallback`
- 正文：`source_snippet > excerpt > editor_summary > fallback_excerpt`

新增了 `source_snippet`，作为比 `editor_summary` 更贴近原始 signal 的事实输入。

### 第二层：去掉原文碎片直出和英文残句

修掉了 live output 中类似：

- `why th。`
- `about ma。`

这类英文截断残句，并对 X / Reddit 的弱标题做了轻量 reader-facing 可读化。

### 第三层：正文整体再详细一档

重点增强了这些 lane：

- `claude-code-watch`
- `codex-watch`
- `openclaw-watch`
- `github-trending-weekly`
- `product-hunt-watch`
- `polymarket-watch`
- `reddit-watch`（高信息长帖命中时）

目标不是“拉长空话”，而是让读者能直接看出：

- 更新对象是谁
- 具体新增/改了什么
- 为什么值得记进日报

### 当前 live smoke 结果（2026-04-13 真数据）

已确认真实输出里：

- `Claude Code` 会写出 `/team-onboarding`、`CLAUDE_CODE_CERT_STORE=bundled`、`brief mode`、`focus mode`、`/ultraplan` 自动建默认 cloud environment
- `Codex` 会写出 `PR #17406`、作者、merge commit、`MCP tool wall time -> model output`
- `OpenClaw` 会写出 `ChatGPT import ingestion`、`Imported Insights`、`Memory Palace`、`structured chat bubbles`
- `Polymarket` 会写出问题本身、领先结果、概率、volume、liquidity、价格变化

### 测试结果

最终本地验证到：

- `23 passed, 11 subtests passed`
- `26 passed, 11 subtests passed`

第三轮完成后已 push：`c7f5c90`。

## 结论 3：Hermes 的 profile 机制是“多实例隔离”，不是“多 agent 集群”

在 `~/.hermes/hermes-agent/hermes_cli/profiles.py` 里确认：

- 每个 profile 都是一个完整独立的 `HERMES_HOME`
- 默认 profile 是 `~/.hermes`
- 命名 profile 在 `~/.hermes/profiles/<name>/`
- 每个 profile 各自有：
  - `config.yaml`
  - `.env`
  - `memories`
  - `sessions`
  - `skills`
  - `logs`
  - `plans`
  - `cron`
  - `gateway.pid`
  - 以及 profile 级 `home/` 用于隔离 git/ssh/gh/npm 等 subprocess HOME

CLI 入口在启动最早阶段通过 `_apply_profile_override()` 解析：

- `hermes -p coder chat`
- `hermes --profile coder gateway`

然后先设置 `HERMES_HOME`，再 import 其他模块，因此 profile 隔离是正式机制，不是临时 hack。

## 结论 4：一台机器上可以跑多个 Hermes

答案是：可以。

但要区分两层：

1. **本地状态隔离**：支持
   - 多个 profile 可以并存
   - 各自拥有独立的 memory / sessions / cron / gateway / logs

2. **外部身份复用**：不一定支持
   - Hermes 有 `acquire_scoped_lock()` / `release_scoped_lock()` 机制
   - 用来防止两个 profile 同时占用同一个外部 bot token / app token / API identity
   - 所以“多 Hermes 同时跑”没问题，但“两个 Hermes 同时挂同一个外部平台身份”会受限

## 结论 5：不同 profile 之间没有原生直接互聊机制

查到的现实边界：

- Hermes 支持 subagent，但那是一次性 task child，不是 profile 级互通
- 没查到原生：
  - cross-profile message bus
  - send_to_profile
  - profile-to-profile 会话桥接
  - 具名 profile agent 互聊协议

因此，两个不同 profile 的 Hermes 若要沟通，现实可行方式是：

1. **共享文件 / 共享仓库 mailbox**（最稳）
2. **cron 串联**（A 产出，B 消费）
3. **经由外部平台间接沟通**（但受 token/identity lock 影响）
4. **一个 profile 调另一个 profile 的 CLI**（把另一个 profile 当外部程序调用）

不应把当前 Hermes profile 机制理解成 OpenClaw 式的“天然多 agent 常驻互聊”。

## 当前判断

### 已完成

- 日报主 cron 已升级到 `daily-report-master-agent`
- 调度已改为每天 06:00
- reader-facing 日报质量基础已明显提升
- profile / 多实例 / 跨 profile 沟通边界已核清

### 仍待做

- 明早 06:00 前，最好手动 run 一次升级后的 cron job，确认：
  - 新 prompt 真的走了 `daily-report-master-agent`
  - Feishu 发布没问题
  - 返回格式符合预期
- 如果后续真要做“双 Hermes 分工协作”，建议先设计共享 mailbox / cron 串联协议，而不是直接假设 profile 之间能原生互聊

## 下一步

1. 手动触发一次 `daily-report-master-0600` cron job 做 smoke run。
2. 如果效果稳定，再决定是否为“日报专用 profile / 主助手 profile”设计共享 mailbox 协议。
3. 如果后面还觉得日报不够细，继续优先在 `daily-report-master-agent` 的 source_snippet / render / tests 这条线上加约束，而不是回退到老 `daily-lane` prompt 修补。