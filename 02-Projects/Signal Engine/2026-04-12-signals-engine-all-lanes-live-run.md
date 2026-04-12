# 2026-04-12 signals-engine 全 lane 实跑记录

- 时间：2026-04-12
- 项目：signals-engine
- 关联仓库：`~/workspace/signals-engine`
- 运行配置：`/tmp/signals-engine-all-lanes-2026-04-12.yaml`
- 运行产物：`/tmp/signals-engine-fullrun-2026-04-12/data/signals`
- 日志目录：`/tmp/signals-engine-fullrun-2026-04-12/logs`

## 结论

我把当前 10 个 lane 都真实跑了一遍：

- 成功产出信号：`github-watch`、`claude-code-watch`、`openclaw-watch`、`codex-watch`、`reddit-watch`、`github-trending-weekly`、`ai-prediction-watch`
- 空跑：`x-feed`、`x-following`、`product-hunt-watch`

关键问题不在单一 lane 崩溃，而在 **运行依赖缺失 + 重新跑同一天同 lane 时不会清理旧 signals + 部分 GitHub 内容抓取存在瞬时 EOF 抖动**。

## 本次实跑结果

- `x-feed` → `empty`，0 signals
  - 根因：缺少 `~/.signal-engine/x-cookies.json`
- `x-following` → `empty`，0 signals
  - 根因：缺少 `~/.signal-engine/x-cookies.json`
- `github-watch` → `success`，37 signals（rerun 后 manifest 统计）
- `claude-code-watch` → `success`，3 signals
- `openclaw-watch` → `success`，3 signals
- `codex-watch` → `success`，23 signals
- `reddit-watch` → `success`，12 signals
- `github-trending-weekly` → `success`，12 signals，3 warnings
- `product-hunt-watch` → `empty`，0 signals
  - 根因：`PH_API_TOKEN` 未设置
- `ai-prediction-watch` → `success`，12 signals

## 发现的问题

### 1. X 两条 lane 在缺 cookie 时仍然返回 `empty`

表现：
- `x-feed` / `x-following` 的 `run.json` 都是 `status: empty`
- 但 `errors` 里明确写着 cookie 文件缺失
- CLI 退出码仍是 0

影响：
- 调度层会把“认证缺失/无法抓取”和“今天真的没有内容”混为一类
- 自动化难以告警

### 2. Product Hunt lane 因 token 缺失被静默跳过

表现：
- `product-hunt-watch` 返回 `empty`
- `warnings` 里记录 `PH_API_TOKEN not set — lane skipped gracefully`

影响：
- 这是刻意设计的 graceful skip，但如果你期望它是生产 lane，就会长期零产出却不报警

### 3. GitHub README 抓取偶发 EOF / reset

表现：
- `github-trending-weekly` 有 3 个 README 抓取 warning
- `github-watch` rerun 时 `openai/codex` 的 changelog 抓取也出现一次 connection reset

影响：
- 主流程能继续，但内容完整性会波动
- 说明 `gh api` / 网络层缺少更稳的重试策略

### 4. 同一天同 lane 重跑不会清理旧 signals，目录会残留陈旧文件

证据：
- `github-watch` rerun 后 `run.json` 统计 `signals_written = 37`
- 但磁盘目录里 `github-watch/2026-04-12/signals/*.md` 实际有 **63** 个文件
- 原因是运行前只 `mkdir(exist_ok=True)`，没有做 lane/date 目录清理

影响：
- rerun 后目录状态与 `run.json` / `index.md` 不一致
- 下游如果按文件夹扫描，会吃到过期或已不在本次结果中的 signal
- 这是当前最值得优先修的运行时一致性问题

### 5. ai-prediction-watch 能跑通，但结果里有一定 query 噪声

表现：
- 产物里混入了 `Style Control On` 这类边缘市场项
- `company-expectation` 组里出现的市场并不都紧贴“公司预期”主线

影响：
- 不是运行失败，是相关性筛选还不够收敛
- 后续应继续压 query pack 和 relevance/filter 规则

## 下一步建议

1. **先修 rerun stale files**
   - collect 前清理 `data/signals/<lane>/<date>/signals/`
   - 或者引入 manifest-based garbage collection
2. **把 X lane 的“缺 cookie”从 `empty` 改成可告警状态**
   - 至少要让调度层能区分“源空”与“源不可用”
3. **为 GitHub 内容抓取补重试 / fallback**
   - 特别是 `gh api repos/.../contents/...` 这条路径
4. **补 Product Hunt token 后再实跑一遍**
5. **继续收窄 ai-prediction-watch 查询和过滤规则**

## 下一动作

- 如果继续推进，优先修：**rerun stale files + X lane 缺凭证状态语义**。
