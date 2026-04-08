# 2026-04-08｜Claude Code / OpenClaw / Codex 三条线横向对照

## 核心判断

今天横向比较了 `anthropics/claude-code`、`openclaw/openclaw`、`openai/codex` 最近一周左右的更新面之后，比较明确的结论是：

> **这三条线的有效更新来源、工程节奏和日报写法都不一样，不能继续用一套统一的 github-watch 规则硬抓。**

它们的差异不是“内容方向略有不同”，而是更接近三种完全不同的仓库类型。

---

## 一、Claude Code：更像产品发布线

## 它像什么

Claude Code 更像一条：

- **面向用户的产品发布线**
- 重心在版本发布、默认行为调整、插件能力、IDE 集成、登录/账号体验、终端交互体验

### 今天这个判断的直接证据

今天专门补拉了 `Claude Code v2.1.94` 的 release/changelog 正文。之前只看 release list 时，会误以为它“只是发了个版本”；但一旦展开 body，内容其实很实：

- 支持 Amazon Bedrock + Mantle
- 默认 effort 从 medium 提升到 high
- Slack MCP send-message 加可点击 channel header
- plugin output styles 支持 `keep-coding-instructions`
- `UserPromptSubmit` hooks 支持 `sessionTitle`
- plugin skill 调用名改为优先使用 frontmatter `name`
- 修复 429 长 Retry-After 时 agent 假卡住
- 修复 macOS Console login 静默失败
- 修复 plugin hooks / `CLAUDE_PLUGIN_ROOT` 相关问题
- 修复 scrollback diff 重复、multiline transcript 缩进、Shift+Space、hyperlink 双开 tab、alt-screen ghost lines、UTF-8 chunk 边界损坏等问题
- VS Code 侧还有 cold-open、dropdown、settings parse warning 等修复

也就是说：

> **Claude Code 的有效信息，主要沉淀在 release note / changelog 正文，而不是零散 PR 流。**

### 对日报的启示

Claude Code 这条线在日报里更适合写成：

- 本版新增了什么能力
- 默认行为或默认策略改了什么
- 哪些修复会直接影响用户日常使用
- 插件 / MCP / VS Code 集成发生了什么变化

### 对抓取规则的启示

Claude Code 最适合：

- `release-first`
- `release body required`
- docs / changelog 高优先级
- PR 只作为补充证据，不作为主输入流

### 不该再做的事情

- 不该只看 release 列表而不展开 body
- 不该把 README diff 当成 Claude Code 的主信号
- 不该试图用 Codex 那套“PR-first”视角去理解它

---

## 二、OpenClaw：更像平台集成 / 多运行时平台线

## 它像什么

OpenClaw 更像一条：

- **agent 平台 / runtime 平台 / 集成平台线**
- 表面多、运行时多、渠道多、安全边界多、配置面多

它不是单点产品功能在迭代，而是在不断调整：

- gateway
- browser
- fetch guard / SSRF
- daemon
- sessions / heartbeat / routing
- env filtering
- integrations（Feishu / Discord / iOS 等）
- provider auth
- compaction / context engine / agent orchestration

### 今天看到的直接信号

最近一批 OpenClaw merged PR 很能说明它的节奏，比如：

- `fix(doctor): warn when stale Codex overrides shadow OAuth`
- `fix(daemon): skip machine-scope fallback on permission-denied bus errors`
- `fix(gateway): stop SSRF guard rejecting operator-configured proxy hostnames`
- `fix(browser): harden SSRF redirect guard`
- `fix(feishu): enforce workspace-only localRoots in docx upload actions`
- `fix(agents): heartbeat always targets main session`
- `feat: add pluggable compaction provider registry`
- `feat: expose prompt-cache runtime context to context engines`

这些更新有个很强的共性：

> **它们往往是平台行为、安全边界、路由逻辑、运行时约束的修正或增强，而不是“用户看一眼就懂的新功能”。**

### 对日报的启示

OpenClaw 这条线在日报里更适合写成：

- 最近平台风险面哪里变了
- runtime / gateway / browser / guard / routing 哪些行为被修正
- 哪些修复会影响 agent 自动化链路稳定性
- 哪些新增能力会改变 operator 或 agent 的实际使用方式

也就是说，OpenClaw 的日报写法更偏：

- 平台变更摘要
- 安全/稳定性/运行时视角
- 行为变化与风险变化

而不是产品发布说明。

### 对抓取规则的启示

OpenClaw 最适合：

- `PR-first`
- `theme clustering required`
- release 作为版本节奏锚点，不是唯一来源

建议后续至少做这几类自动主题归类：

- 安全 / 权限
- runtime / daemon / gateway
- browser / web guard
- agents / sessions / heartbeat
- integrations
- config / auth / env

### 不该再做的事情

- 不该只盯 release title
- 不该把 OpenClaw 当成“发版说明仓”去抓
- 不该没有主题归类就直接把 PR 生拼成日报

---

## 三、Codex：更像底层 runtime / execution substrate 线

## 它像什么

Codex 是这三条里最不像“普通产品仓”的。

它更像：

- **agent runtime / execution substrate / execution core**
- 持续演进的底层执行内核

最近一周里，它的 merged PR / commit 主线已经很明显，主要集中在：

- remote / executor filesystem
- app-server / protocol / realtime
- TUI startup / Fast Mode / auth / UX
- MCP / guardian / approvals / multi-agent
- sandbox / Windows / Bazel / DB / network policy

### 今天看到的直接信号

最近这一批高价值 PR 很典型：

- `#17039` `fix(tui): reduce startup and new-session latency`
- `#16949` `Use model metadata for Fast Mode status`
- `#17048` `[codex] Apply patches through executor filesystem`
- `#16960` `Add WebRTC transport to realtime start`
- `#17044` `introduce generic ServerResponse`
- `#16973` `Allow enabling remote control in runtime`
- `#16465` `MCP Apps part 2`
- `#16956` `guardian transcript` 修复
- `#16946` `danger-full-access denylist-only network mode`

这类内容有一个鲜明特点：

> **它们几乎不会被 README/CHANGELOG 很好承载，真正的信息都在 PR title / body / commit message 里。**

### release 的位置

Codex 的 release 很频繁，能说明节奏很快，但 release body 往往并不提供足够多的更新细节。所以 release 更像：

- 节奏参考
- 版本推进信号
- 而不是正文主来源

### 对日报的启示

Codex 的日报更适合写成：

- 最近几条底层主线在往哪推进
- remote / executor / app-server / MCP / guardian / sandbox / TUI 分别发生了什么变化
- 哪些更新说明 runtime 边界在收口
- 哪些更新是用户可感知的交互修复，哪些是架构/协议层演进

也就是说，Codex 的日报更像：

- 工程演进日报
- runtime 主线摘要
- 协议 / 执行 / 治理面的进展汇总

### 对抓取规则的启示

Codex 最适合：

- `merged-pr-first`
- `commit-second`
- `release-third`
- `body-summary extraction required`

理想 signal 最好能提取：

- PR title
- merged_at
- URL
- body summary
- validation/testing block
- area/theme tag

### 不该再做的事情

- 不该让 README/CHANGELOG 成为 Codex 的主来源
- 不该只看 release tag 就试图写日报
- 不该把它当成 Claude Code 那种 release-first 仓来处理

---

## 四、三条线的一句话区别

### Claude Code

> **看 release note，像看产品版本说明。**

### OpenClaw

> **看 PR 流，像看一个多运行时平台的行为变化和安全/稳定性收口。**

### Codex

> **看 PR/commit 流，像看一个 execution runtime 的底层演进。**

---

## 五、为什么不能继续用一套统一规则抓三家

如果继续统一用：

- release
- changelog
- README

这套规则去抓三条线，就会出现非常典型的问题：

### 对 Claude Code
- 还有可能勉强够用
- 但前提是必须展开 release body

### 对 OpenClaw
- 会漏掉很多真正重要的平台行为变化
- PR 流里的关键信号会被压没

### 对 Codex
- 基本会被抓歪
- 最后只剩 README/CHANGELOG 噪声
- 真实更新主线完全看不见

所以问题已经不应该被表述为：

> “优化 github-watch”

而更准确应该表述为：

> **为重点 repo 引入 repo-specific watch strategy。**

---

## 六、建议直接抽成三种模板

## 模板 A：产品发布型

适用：
- Claude Code

规则：
- release-first
- release body 必拉
- docs/changelog 高优先级
- PR 低优先级补充

---

## 模板 B：平台集成型

适用：
- OpenClaw

规则：
- merged PR 主输入
- 强制主题聚类
- release 做版本锚点
- 重点强调安全 / routing / gateway / browser / integrations / runtime 行为变化

---

## 模板 C：底层 runtime 型

适用：
- Codex

规则：
- merged PR + commits 主输入
- release 做节奏参考
- README/CHANGELOG 降权
- signal schema 要更 rich，能支持工程主线级写法

---

## 七、如果后续要排改造顺序

今天的判断下，我会建议按下面顺序改：

### 1. 先改 Codex
原因：
- 被现有规则误伤最严重
- 收益最大
- 也是最能证明 repo-specific strategy 必要性的样本

### 2. 再改 Claude Code
原因：
- 主要补上 release body 强制拉取即可
- 相对容易快速见效

### 3. 最后改 OpenClaw
原因：
- 需要主题聚类
- 不是抓不到，而是“怎么组织”更难
- 工程复杂度略高，但长期价值很大

---

## 一句话收束

今天这个横向结论，真正重要的不是“这三家风格不同”这么泛，而是：

> **Claude Code、OpenClaw、Codex 对应的是三种不同的观察模板：产品发布型、平台集成型、底层 runtime 型。后续如果继续用一套统一抓法做 GitHub AI Daily，质量只会持续不稳定；必须引入 repo-specific watch strategy。**
