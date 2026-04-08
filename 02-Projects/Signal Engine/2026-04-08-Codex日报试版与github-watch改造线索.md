# 2026-04-08｜Codex 日报试版与 github-watch 改造线索

## 这次要记录的核心结论

今天围绕 daily-lane、github-watch、Codex 更新抓取方式做了几轮排查和试写，最终比较明确的结论有三组：

1. **daily-lane 早晨那次“今日无可用 signals”不是 collector 没产出，而是 orchestrator 发现规则写错了目录结构。**
2. **`github-watch` 现在对重点 repo（尤其 `openai/codex`）的信号面过窄，而且对低信息量 diff 缺乏降噪。**
3. **如果改用 merged PR / commit / release 作为 Codex 的主信号源，日报成品会明显比 README/CHANGELOG 驱动的版本更有信息量。**

---

## 一、daily-lane 当天异常的真实原因

### 现象

2026-04-08 08:30 那次 orchestrator 给出的结果是：

> 今日无可用 signals，未生成日报

但本地真实产物检查后发现，当时并不是没有 signals。

### 实际存在的 lane 产物

2026-04-08 当天实际已存在：

- `~/.daily-lane-data/signals/github-watch/2026-04-08/index.md`
- `~/.daily-lane-data/signals/github-trending-weekly/2026-04-08/index.md`
- `~/.daily-lane-data/signals/product-hunt-watch/2026-04-08/index.md`
- `~/.daily-lane-data/signals/x-following/2026-04-08/index.md`

当时唯一缺的是：

- `x-feed/2026-04-08/index.md`

### 根因

orchestrator prompt 把 signals 目录假设成：

- `/Users/haha/.daily-lane-data/signals/YYYY-MM-DD/`

但真实结构一直是：

- `/Users/haha/.daily-lane-data/signals/<lane>/YYYY-MM-DD/index.md`

而且对比 04-07 也确认了：

- 04-07 和 04-08 的目录结构是一致的
- 并不是今天 collector 突然换了输出位置
- 错的是 orchestrator 对目录结构的假设

### 已处理

已修改：

- `~/workspace/daily-lane/docs/prompts/orchestrator-v0.2.md`

把发现规则改成按：

- `signals/<lane>/<date>/index.md`

来找 lane index。

---

## 二、x-feed 当天为什么会缺失，以及怎么补上的

### 当时情况

04-08 当天其他 4 条 lane 已有 signals，只有 `x-feed` 缺失。

### 排查结果

第一次手动跑 `x-feed` 时，老 shell lane 在不完整环境下暴露出一个问题：

- `opencli twitter timeline` 返回内容里含控制字符
- shell lane 直接喂给 `jq`
- 最终导致空 index / 异常写出

这说明老 `x-feed` shell 版链路本身比较脆，不适合作为长期稳定路径。

### 最终处理结果

用正确入口重新补跑后，成功生成：

- `~/.daily-lane-data/signals/x-feed/2026-04-08/index.md`
- `~/.daily-lane-data/status/x-feed/2026-04-08.json`

结果：

- 100 条 feed exposures
- 81 个作者
- `status=success`

随后 2026-04-08 的整份 daily-lane 日报也已重建完成，并成功导入飞书文档。

---

## 三、为什么现在的 github-watch 总让 Codex 看起来“没更新”

用户这次明确指出一个问题：

> `codex` 明明一直在更新，但日报里经常看起来像“没拉到”；而 `claude-code` 又经常只剩 README 变化。

核对 2026-04-08 当天的 `github-watch` 输入后，发现：

### 1. Codex 不是没抓到，而是抓到的多是低信息量 diff

当天 `openai/codex` 实际抓到了两条：

- `changelog`
- `readme`

但打开原始 signal 后发现，这两条都只是非常轻微的格式/排版修正：

- 一行换一行
- 链接说明微调
- 没有真正的功能更新信息

所以 editor 最后才只能写成：

> 没有实质功能变化

### 2. Claude Code 当天确实只有一条 README signal

`anthropics/claude-code` 当天 signal 输入里就只有：

- 一条 README 变化

而且打开原始 diff 后看，本质上也是条款/排版层级的调整，不是功能级更新。

### 3. 真正的问题在 signal 面和降噪策略

这次可以比较明确地把问题拆成两层：

#### 问题 A：repo 的主获取面配错了

对于 `openai/codex` 这种仓，README/CHANGELOG 不是主更新面。

#### 问题 B：低信息量 diff 没被很好降噪

像下面这类变更，理论上不应该被当成日报主信号：

- 纯 markdown 换行
- 纯 license 文本整理
- 1 行格式修正
- 链接排版改动

也就是说，现在这条链路不是“完全没抓到”，而是：

> **抓到的面不对，而且抓到后没有把低价值 diff 过滤掉。**

---

## 四、Codex 的更新记录，真正应该从哪里抓

今天专门对 `openai/codex` 最近 30 天的更新方式做了调查，包括：

- GitHub releases
- merged PR
- commits
- `last30days` 社交研究法

### 最终结论

对 Codex 来说，主优先级应该是：

1. **merged PR**
2. **main 分支 commits**
3. **releases（更多是版本节奏，不是细节正文）**
4. README / CHANGELOG 只做补充，不应再当主来源

### 为什么

#### 1. release 很频繁，但正文很薄

最近 7 天里，Codex 发版非常密：

- `rust-v0.119.0-alpha.2`
- `...alpha.3`
- `...alpha.4`
- 一直到 `...alpha.17`

这说明它更新很勤，但 release body 常常只有一句：

- `Release 0.119.0-alpha.xx`

所以：

- **release 能说明版本节奏快**
- 但不太能单独说明“到底改了什么”

#### 2. 真正的更新细节主要在 PR / commit message

这次拉出来的一批高信息量更新，几乎都来自 merged PR / commit：

- `#17039` `fix(tui): reduce startup and new-session latency`
- `#16949` `Use model metadata for Fast Mode status`
- `#17048` `[codex] Apply patches through executor filesystem`
- `#16960` `Add WebRTC transport to realtime start`
- `#16973` `app-server: Allow enabling remote control in runtime`
- `#16465` `[mcp] Support MCP Apps part 2 - Add meta to mcp tool call result`
- `#16956` `fix(guardian): don't throw away transcript when over budget`
- `#16946` `Add danger-full-access denylist-only network mode`

这些主题如果只靠 README/CHANGELOG，基本看不到。

### `last30days` 这次反而证明了什么

今天还特意用 `last30days` skill 跑了一次 Codex 最近 30 天的 update history。结果很一般：

- Reddit：0
- X：0（未认证）
- YouTube：只回到一条 Cursor 2.0 视频
- HN / Polymarket：0

这个结果的意义反而很明确：

> **Codex 的高价值更新并不主要沉淀在社交讨论面，而是沉淀在 GitHub 工程面。**

所以，如果目的是做“Codex 更新日报”，与其优先依赖社交面，不如直接抓 GitHub PR / commit / release。

---

## 五、已经试写过一版“如果用 PR/commit/release 做 Codex 日报”会长什么样

今天已经按下面这个口径，试写过一版 Codex 单仓日报试稿：

- 时间窗：最近 7 天
- 风格：中文正式日报风格
- 底层素材：只用 merged PR / commit / release
- 不再依赖 README/CHANGELOG 当主体

### 试写后的感受

这版明显比 README/CHANGELOG 驱动的结果更像“能看的日报”，因为它能组织出真正的工程主线：

#### 主线 1：remote / executor filesystem
- `apply_patch` 往 executor filesystem 迁
- local / remote workspace 语义边界在收口

#### 主线 2：app-server / realtime / protocol
- WebRTC transport
- `ServerResponse`
- remote control / fs watch / exec 输入校验

#### 主线 3：TUI / 交互体验
- startup latency
- Fast Mode status
- device-code auth
- CJK 输入与 resume picker

#### 主线 4：MCP / guardian / approvals
- MCP Apps
- `/mcp` inventory listing 提速
- guardian transcript 不再“超预算就丢空”
- allowed approval reviewers

#### 主线 5：Windows / Bazel / sandbox / CI 收口
- Windows Bazel trust tests
- platform-specific env vars
- bwrap / Seatbelt / network mode / sqlite / bazel cache 等底层兼容性问题

这说明：

> **如果后续真要把 `github-watch` 改好，目标不该只是“多抓一点 Codex 信息”，而是要让 signal 结构能支持这种层次的日报成稿。**

---

## 六、明天要继续看的改造方向

这条记录的真正用途，是给明天的设计和改造做起点。

### 明天重点应该讨论的，不是“要不要改”，而是“怎么改”

建议明天直接围绕这几个问题推进：

### 1. `github-watch` 对重点 repo 是否需要 repo-specific watch rule？
对于 `openai/codex`，很可能要单独改成：

- release: 开，但不做主正文
- merged PR: 主来源
- commits: 主来源或高优先级补充
- README: 低优先级
- CHANGELOG: 低优先级

### 2. signal 层是否需要“低信息量 diff 降噪”
例如：

- 仅空白/换行变化
- 仅 license / plan 名称修正
- 只有 1 行 markdown 调整

这类变更应考虑：

- 不生成正式 signal
- 或生成 low-priority signal，不进入日报主候选

### 3. signal 的 schema 是否需要支持 PR/commit richer fields？
如果要让日报写出“像样的 Codex 工程更新”，signal 里最好能保留：

- PR title
- merged_at
- URL
- body summary
- validation/testing block
- changed area / theme tags

否则 editor 还是只能写成单薄摘要。

### 4. `claude-code` 也应该做一次同样的获取面检查
今天主要聚焦在 Codex，但同样的问题也可能存在于 Claude Code：

- 它真正的更新是不是也不在 README？
- 它应该抓 release / docs / PR / commits 的哪一层？

这个明天可以和 Codex 一起看，最后形成重点 repo 的差异化抓取策略。

---

## 一句话收束

今天最关键的结论，不是“Codex 这周更新很多”这么简单，而是：

> **如果要让 GitHub AI Daily 对重点 repo 真的有用，`github-watch` 必须从“统一抓 README/CHANGELOG”升级成“重点 repo 按真实更新面抓信号”，而 `openai/codex` 就是最典型、最值得先改的样本。**
