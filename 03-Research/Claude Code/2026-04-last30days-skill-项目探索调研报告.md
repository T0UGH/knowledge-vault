---
title: last30days-skill 项目探索调研报告
date: 2026-04-05
tags:
  - last30days-skill
  - project-research
  - research
  - lane
  - Obsidian
status: done
source_files:
  - /Users/haha/.openclaw/workspace/last30days-skill/README.md
  - /Users/haha/.openclaw/workspace/last30days-skill/SKILL.md
  - /Users/haha/.openclaw/workspace/last30days-skill/SPEC.md
  - /Users/haha/.openclaw/workspace/last30days-skill/TASKS.md
  - /Users/haha/.openclaw/workspace/last30days-skill/.claude-plugin/plugin.json
  - /Users/haha/.openclaw/workspace/last30days-skill/scripts/last30days.py
  - /Users/haha/.openclaw/workspace/last30days-skill/scripts/briefing.py
  - /Users/haha/.openclaw/workspace/last30days-skill/scripts/store.py
  - /Users/haha/.openclaw/workspace/last30days-skill/scripts/watchlist.py
related_notes:
  - 03-Research/Claude Code/2026-04-last30days-skill-快速阅读-哪些设计值得抄到lane上.md
---

# last30days-skill 项目探索调研报告

## 0. 这篇报告在回答什么

这篇不是“怎么安装 last30days-skill”的说明文，也不是一次轻量 README 导读，而是一次更偏决策视角的项目探索。

要回答的核心问题是：

1. `mvanhorn/last30days-skill` 现在到底是一个什么级别的项目；
2. 它的真正产品定位是什么，不是什么；
3. 它和我们现在的 `lane / signal / daily report` 体系是什么关系；
4. 值不值得继续投入时间；
5. 如果继续，最合理的接入姿势是什么。

这次调研主要基于四类材料：

- 仓库文档：`README.md`、`SKILL.md`、`SPEC.md`、`TASKS.md`
- 关键实现：`scripts/last30days.py`、`briefing.py`、`store.py`、`watchlist.py`
- 本机实测：`--diagnose`、最小真跑、first-run 问题修复、当前可用源状态
- 已有上下文笔记：`2026-04-last30days-skill-快速阅读-哪些设计值得抄到lane上.md`

先给结论版：

> **`last30days-skill` 不是一个适合直接塞进主 lane 的“稳定召回层”，而是一个已经做得相当完整的“30 天社区语境研究引擎 / 研究前置层能力包”。**
>
> **它最大的价值不在“替代 lane”，而在“给 lane 前面补一层高密度、跨平台、面向议题判断的研究入口”。**

如果再压缩一句：

> **它值得继续留在本机并持续使用，但应该被当成“研究工具”和“辅助判断层”，而不是“主生产链路”。**

---

## 1. 项目是什么：不是单个 skill，而是一个带产品壳的研究引擎

如果只从名字看，`last30days-skill` 很容易被理解成“一个帮 Claude Code 做近 30 天检索的小技能”。但从仓库结构看，它其实已经明显超出“技能脚本”这个级别。

文档层面，它至少已经具备一个独立能力包该有的基本外壳：

- `README.md`：完整安装、配置、能力说明、模式说明、演示案例
- `SKILL.md`：面向 agent runtime 的行为规约、first-run 流程、交互规范、输出模板
- `SPEC.md`：把问题收敛到清晰的架构与模块边界
- `TASKS.md`：实现任务分解
- `.claude-plugin/plugin.json`：明确的插件元数据

实现层面，它也不是“一个大脚本”，而是很清楚的 orchestrator + lib 分层：

- `scripts/last30days.py` 是总调度器
- 各数据源、归一化、打分、去重、渲染、配置、模型选择等逻辑都下沉到 `scripts/lib/*`

换句话说，这个项目的真实定位更像：

> **一个被包装成 Claude skill / Claude plugin / Codex skill 的多源研究引擎。**

这点非常重要，因为它决定了后续判断：

- 如果把它当“一个 skill”，会低估它；
- 如果把它当“一个能替代现有 lane 的成品系统”，又会高估它。

更准确的理解应该是：

> **它是一个已经有产品意识、工程意识和分发意识的 research engine。**

---

## 2. 它解决的核心问题是什么

`README.md` 里写得很明确：它想解决的不是“全网搜索”，也不是“权威知识库问答”，而是一个更具体的问题：

> **当用户想知道“过去 30 天人们到底在讨论什么、推荐什么、在赌什么、在视频里怎么说”时，能不能用一个统一的研究入口，把这些公共讨论面抓回来，再做一轮去重、排序、综合。**

它抓的不是传统文档优先，而是明显偏“社区信号优先”：

- Reddit
- X / Twitter
- YouTube
- TikTok
- Instagram
- Hacker News
- Polymarket
- Web search
- Bluesky / Truth Social / Xiaohongshu（按配置能力逐步打开）

也就是说，它最强的问题域不是“查知识”，而是：

- 最近一个工具 / 产品 / 人 / 话题，社区怎么聊；
- 对比型问题近 30 天怎么分化；
- 某个新方向是不是开始形成稳定叙事；
- 视频博主、Reddit 讨论、HN、预测市场之间是否出现共振；
- 用户写文章、判断选题、判断工具热度时，有没有更快的“近实时语境切片”。

这和传统搜索、RAG、知识库的区别很大。

它不是在回答：

- “这个概念的标准定义是什么”；
- “这篇论文的结论是什么”；
- “官方文档怎么说”。

它更像在回答：

- “最近 30 天真正有声量的说法是什么”；
- “大家在哪些维度分歧最大”；
- “现在的主流工作流叙事正在往哪边收敛”。

这也是为什么它对你有吸引力：

你本来就在做 AI coding 生态、工具变迁、工作流判断、公众号选题和日报信号组织。这类工作最怕的是只看官方文档，不看真实社区语境。

从这个角度说，`last30days-skill` 的问题定义是对的，而且和你当前的任务形态是贴合的。

---

## 3. 架构上最值得注意的地方

## 3.1 orchestrator + pipeline，而不是“搜一下然后让模型总结”

`scripts/last30days.py` 的价值，不只是把多个数据源调起来，而是它在组织一条比较完整的研究流水线。

从 `SPEC.md` 和主脚本都能看出来，它有一套明确的数据流：

- 发现（discovery / search）
- 富化（enrichment）
- 归一化（normalize）
- 打分（score）
- 去重（dedupe）
- 渲染（render）
- 可选入库（store）

这比很多“AI research tool”更扎实的地方在于：

> **它没有把所有质量问题都推给最终的大模型总结，而是在前面的脚本层先做了大量结构化约束。**

这跟我们给 lane 定下的原则高度一致：

- 召回层尽量少放 AI 决策
- 先做稳定、可回放、可解释的脚本管线
- AI 更适合放在后处理、解释、综合层

所以从工程方法论看，这个项目是“同路人”。

## 3.2 progressive unlock 设计得很聪明

`README.md` 和 `SKILL.md` 的一个明显优点，是它没有要求用户一步把所有密钥、账号、外部能力都配齐，而是设计成分级解锁：

- 零配置就能跑 Reddit public + HN + Polymarket
- 浏览器 cookie 解锁 X
- `yt-dlp` 解锁 YouTube
- `SCRAPECREATORS_API_KEY` 一把钥匙解锁 Reddit comments + TikTok + Instagram
- 其他 web / Bluesky / TruthSocial 再逐步打开

这带来两个好处：

1. **降低试用门槛**：用户很快就能验证“这个东西对我有无价值”；
2. **把复杂性延迟到真正需要的时候**：不是第一步就被接入成本劝退。

从产品体验上说，这比很多“先配一堆 key 才能动”的项目成熟得多。

## 3.3 watchlist / briefing / store 把 one-shot research 往长期积累推进了一步

如果只看 `/last30days <topic>`，它像一个一次性研究工具。

但 `briefing.py`、`store.py`、`watchlist.py` 已经说明作者在往“长期积累”方向走：

- `store.py`：SQLite 落库、WAL、安全并发、FTS5 搜索
- `watchlist.py`：主题 watchlist、预算控制、run-one / run-all
- `briefing.py`：从累计 findings 里生成 daily / weekly briefing 数据

这说明它已经不是“只会临时搜一下”，而是有意往下面这个方向演进：

> **一次研究 → 持续观察 → 历史累积 → briefing 输出**

这条路径对 OpenClaw / always-on 环境其实很有吸引力，因为它天然适合被 cron 或 bot 包起来。

但注意，这里也恰恰藏着它的边界：

> 它已经摸到了“研究操作系统”的边，但还没有长成真正稳定的长期情报系统。

---

## 4. 这次本机实测，说明了什么

今天这轮接入和实测，有几个事实值得单独记下来。

## 4.1 它不是纸面项目，是真的能跑起来

本机路径：

- 工作副本：`/Users/haha/.openclaw/workspace/last30days-skill`
- 本地 skill 软链：`~/.claude/skills/last30days -> /Users/haha/.openclaw/workspace/last30days-skill`

我已经完成过两件更关键的事：

1. `--diagnose` 真跑
2. 最小 research 真跑（`Claude Code vs Codex`）

这说明它不是“README 看着不错但本地跑不起来”的仓库，而是至少已经过了第一道门槛：

> **真实环境里，零配置/低配置模式下可以产出有价值结果。**

## 4.2 first-run 上确实存在一个真实但不大的产品坑

这次本机排查发现一个很具体的问题：

- `INCLUDE_SOURCES` 缺省值在 `env.py` 里是 `None`
- 主流程会直接对它 `.split(',')`
- 于是 first-run / 最小真跑时会在配置逻辑里炸掉

这个问题的性质我会这样判断：

- **不是架构级错误**
- **也不是研究质量问题**
- 而是一个典型的“产品最后 10 米打磨还不够”问题

也正因为它很具体，所以反而说明这个项目已经进入了一个比较真实的阶段：

> 不是停留在 PoC，而是在跟真实 first-run 体验较劲。

而且它是可修、可验证、可回归的。今天已经按 TDD 补测试并修掉，本机验证通过。

## 4.3 当前这台机器上的最低可用层已经够做第一类价值验证

当前本机 `--diagnose` 结果显示可用源主要是：

- Reddit（public threads）
- YouTube
- Hacker News
- Polymarket

未启用的主要是：

- X/Twitter
- Reddit comments 深抓
- TikTok / Instagram
- native web search backend

这意味着：

> **现在这份本地安装，已经足够承担“第一轮工具价值验证”与“轻量社区语境研究”。**

但也意味着：

> **它当前还不适合被拿来做你最重的、必须覆盖全面的长期情报主链。**

因为缺了 X 和 Reddit comments，很多真正高价值的社区语境还没完全打开。

---

## 5. 项目当前最强的地方

## 5.1 它抓住了一个真实痛点，而且切得很准

很多人都知道“要做 research”，但真正麻烦的是：

- 不同平台入口分散
- 信号质量差异很大
- 最近 30 天和更长周期混在一起
- 论坛、视频、市场、社交平台的表达方式完全不同
- 研究结论常常只是“我搜了几篇网页”

`last30days-skill` 最有价值的一点，是它明确站在“近 30 天公共语境聚合”的问题上，不假装自己是万能搜索。

这个切口清楚，反而更容易做出产品感。

## 5.2 研究输出不是只给“答案”，而是给“接下来的对话位”

从 `SKILL.md` 的设计可以看出，它不是只想产出一段总结，而是把自己设计成一种连续交互能力：

- 先研究
- 再总结 patterns
- 再根据 query type 给 recommendations / comparison / prompting / general follow-up
- 再继续回答后续问题或生成 prompt

这套交互设计有点像“把研究结果变成当前对话的即时上下文缓存”。

对用户体验是有帮助的，因为很多研究并不是“读完一份报告就结束”，而是：

- 先扫一遍
- 再追问一个方向
- 再把结果转成 prompt / 决策建议 / 选题

## 5.3 工程卫生比很多同类项目好

这点在前一篇快速阅读里已经提过，这里只补一句更直白的判断：

> **它不像一个“堆功能的 AI side project”，而更像一个认真维护中的工具产品。**

几个明显信号：

- README 写得完整，不是只给开发者自己看
- SPEC / TASKS 不是摆设
- 测试目录规模不小（本机 `tests/` 43 个文件；README 明写 455+ tests）
- 有 mock / fixtures / diagnose / config layering
- 有 open variant、watchlist、briefing 这种产品路径延展

这说明它至少值得继续观察，而不是“一眼略过”。

---

## 6. 项目当前最明显的短板和风险

## 6.1 它最容易被误用成“主链系统”

这是我认为最大的风险。

`last30days-skill` 做得越完整，越容易让人产生一种错觉：

> 既然它已经有 search、score、dedupe、store、watchlist、briefing，那是不是可以直接把它当成整个情报系统了？

我倾向于明确反对这个判断。

因为它本质上还是：

- 以“topic 驱动的一次研究”起家
- 以“讨论聚合 + 结果综合”为主要输出
- 更偏问答 / 研究入口，而不是稳定采集底座

而我们的 lane / signal 体系强调的是：

- 更稳定的脚本收集
- 更无损的 signal 保留
- 更少的前置 AI 决策
- 更适合长期跑批和回溯

这两者不是一个层。

所以如果强行把它推成主链，问题会马上出现：

- topic 触发机制怎么定义？
- 什么值得跑、什么不值得跑？
- 结果怎么结构化落进现有 signal / lane / daily report？
- 一次研究和持续收集的边界在哪？
- 当数据源覆盖不完整时，谁来兜底？

结论是：

> **它很适合补在主链前后，但不适合直接替代主链。**

## 6.2 数据源覆盖质量高度依赖外部配置

这个问题不是它独有，但对它影响很大。

它的产品宣传很依赖“跨平台多源研究”，但现实中不同用户的可用源会差很多：

- X 没有 cookie / xAI key，就缺一大块
- 没有 ScrapeCreators，就拿不到 Reddit comments / TikTok / Instagram 的更深层内容
- 没有 web backend，网页补充层也会弱

这意味着实际研究质量会随着配置水平急剧波动。

对重度使用者来说，这不是致命问题；
但对产品化扩散来说，这是一个真实门槛。

## 6.3 研究输出的结构化可复用性还不够“系统级”

它能输出：

- compact report
- markdown
- JSON
- context snippet
- briefing data

这些已经不错。

但如果站在我们自己的系统要求上看，它还缺几件更强的能力：

- 更严格、稳定的 signal schema
- 对 release / changelog / README diff 这种“对象级变化”的长期追踪
- 更强的 topic state / run state / evidence trace 管理
- 更适合跨天多任务编排的显式状态交接

换句话说，它是“研究工具”级别的结构化，不是“情报基础设施”级别的结构化。

---

## 7. 它和 lane 的关系，应该怎么摆

这是这篇报告里最重要的判断之一。

我的建议非常明确：

## 7.1 不要把它当 lane 主链

原因前面已经讲了，本质上是边界不对。

lane 更像：

- 稳定脚本
- 无损信号收集
- 数据结构清楚
- 低波动、可排程、可回放

而 `last30days-skill` 更像：

- 针对某个 topic 的深一层研究器
- 社区讨论与内容语境聚合器
- 研究前的“情报放大镜”

所以它不应该替代：

- `github-watch`
- release/changelog/README 变化采集
- 你已有的 daily report 主流程

## 7.2 把它放到“研究前置层 / 文章前置层 / 选题验证层”更合适

它最适合的几个场景，我现在判断已经很清楚了：

### 场景 A：新工具 / 新项目初筛
例如：
- 某个 agent orchestration 项目值不值得跟
- 某个新出的 coding tool 到底是真热还是一阵风
- 社区是不是已经开始形成稳定工作流叙事

### 场景 B：写公众号前先扫近 30 天语境
例如：
- “后端工程师怎么用 AI coding”
- “Claude Code vs Codex 的真实分工”
- “agent orchestration 为什么开始从炫技转向控制面”

这时它能快速帮你拿到：
- 近期高频说法
- 争议点
- 不同平台的叙事差异
- 可作为文章素材的原话与角度

### 场景 C：日报之外的议题补研
日报主链更适合稳定、批量、结构化地扫核心 repo 与固定 watchlist。

但一旦出现“这个方向值得深挖吗？”这种问题，`last30days-skill` 就很合适做补充研究。

也就是：

> **日报管稳定主线，last30days 管临时议题和社区语境放大。**

## 7.3 它更像“研究工具箱里的专业工具”，不是“总控台”

这是最后再强调一次边界：

- **不是总控台**
- **不是主采集层**
- **不是日报主引擎**
- **是高价值、按需调用的研究工具**

这个位置摆对了，它就很有用；摆错了，反而会让系统复杂度上升。

---

## 8. 从你的工作流看，它最值得怎么用

结合你当前的工作重心，我会把它的优先级排成下面这样。

## 第一优先级：AI coding 生态研究前置层

这是它现在最适合你的地方。

你正在持续关心：

- Claude Code
- OpenClaw
- Codex
- agent orchestration
- coding workflow
- review loop
- team / teammate / runtime 这类新结构

这些问题一个共同点是：

> **光看源码或官方文档不够，必须知道社区最近 30 天到底怎么谈。**

而 `last30days-skill` 恰好就是干这个的。

## 第二优先级：写作选题验证器

如果你后面继续做公众号，这个工具很适合放在“开始写之前”的那一步。

你先拿一个题去扫：

- 有没有足够多的讨论
- 争议点在哪
- 哪些观点已经太陈词滥调
- 有没有值得拿来对比的真实案例

它不能替你写出好文章，但能大幅降低“选题判断失真”的概率。

## 第三优先级：新项目 / 新方向值得不值得跟踪

比如遇到一个你不熟的新项目时，不是马上塞进长期 watchlist，而是先用它扫一下近 30 天：

- 社区有没有真反馈
- 开发者群体是在夸、在吐槽，还是在围观
- 视频圈、HN、Reddit 是不是同步出现
- 是否有 cross-platform signal

这会比只看 GitHub stars 更立体。

---

## 9. 下一步如果继续投入，最合理的路线

我建议分三层，不要一步到位。

## 9.1 第一层：继续当本地研究工具使用

这一步已经完成得差不多了：

- 已安装到本地 skill 路径
- 已补最小配置
- 已修复一个 first-run 问题
- 已能真跑

下一步只需要积累真实使用样本：

- 连跑 10~20 个你真实关心的话题
- 记录哪些题型回报最高
- 记录哪些情况下因为缺 X / comments 导致价值明显下降

先用真实 usage 验证，不急着重构系统。

## 9.2 第二层：总结“适用题单”和“禁用题单”

也就是明确它适合什么，不适合什么。

例如：

### 适合
- 工具对比
- 近期工作流讨论
- agent / orchestration 生态扫描
- 公众号选题前置扫语境
- 新项目值不值得继续跟

### 不适合
- 需要强官方文档权威性的知识问答
- 需要精确时间序列和对象级变化跟踪的长期系统
- 需要高稳定可重复结构化输出的日报主链

把这个边界固化下来，才不会后面越用越混。

## 9.3 第三层：如要接 lane，只接成“辅助分支”

如果以后真要接入现有系统，我建议只接成辅助分支，比如：

- `research-on-demand`
- `topic-scout`
- `article-preflight`
- `trend-scan`

也就是：

- 由人或上游脚本显式触发
- 研究结果落到独立目录 / 独立 store
- 与主 lane 的 signal 保持松耦合
- 最多把高价值结论回写到知识库，而不是直接喂回主采集层

这个边界很重要。

---

## 10. 最终判断

如果只让我给一个简短判断，我会给这个版本：

> **`last30days-skill` 是一个值得继续留在本机、持续使用、但不该被高估成主系统的项目。**

更展开一点：

### 我认可的地方
- 问题定义对
- 产品切口清楚
- progressive unlock 做得聪明
- 工程骨架比同类项目扎实
- 已经不是纸面 PoC，而是能跑、能修、能演进的真实工具
- 对你当前的 AI coding 生态研究非常贴合

### 我保留的地方
- 研究质量高度依赖外部配置
- 更像 topic research engine，不是稳定主链
- 长期结构化能力还没到“情报基础设施”级别
- 如果误当总控台，会把系统复杂度带偏

### 结论性的定位建议

> **继续用，但把它牢牢放在“研究前置层 / 语境放大器 / 选题验证器”的位置。**

不要让它承担 lane 主链的职责；
但在你需要判断“社区最近到底怎么说”时，它非常值得成为默认工具之一。

---

## 11. 可直接执行的后续动作

### 方案 A：低风险继续验证
1. 连续跑 10 个你真实关心的题
2. 记录回报最高的 3 类题型
3. 记录最受限的缺失源（大概率是 X / Reddit comments）
4. 再决定是否补全配置

### 方案 B：形成正式使用规范
1. 写一篇 `last30days-skill 使用边界与题型清单`
2. 固化哪些问题默认先跑 `last30days`
3. 固化哪些问题不应该跑它，而应该走别的链路

### 方案 C：接成 lane 辅助分支
1. 不改主 lane
2. 新建一个按需触发的 `topic-scout` / `trend-scan`
3. 只把研究结论沉淀到知识库，不直接混入主 signal 层

我的建议顺序是：

> **先 A，再 B，最后才考虑 C。**

现在最不该做的事，就是因为它今天看起来挺好用，就直接把它硬塞进主链。

---

## 附：这次调研里的几个关键事实

- 仓库当前远端：`https://github.com/mvanhorn/last30days-skill.git`
- 本机工作副本：`/Users/haha/.openclaw/workspace/last30days-skill`
- 本机 skill 软链：`~/.claude/skills/last30days`
- 近 5 个本地 commit（2026-04-05 查看）：
  - `61904b3 feat: INCLUDE_SOURCES config + TikTok/Instagram opt-in in NUX`
  - `bb79d54 Revert "fix: ScrapeCreators modal sells all 5 platforms, not just Reddit"`
  - `2eb9cd6 fix: ScrapeCreators modal sells all 5 platforms, not just Reddit`
  - `775596c feat: v2.9.6 — free-first NUX, cookie extraction, quality scoring`
  - `4d6224f feat(youtube): extract transcript highlights like Reddit comment gems`
- 本机仓库测试目录文件数：`43`
- `README.md` 明写：`455+ tests`
- 本机当前 `--diagnose` 可用源：Reddit public、YouTube、HN、Polymarket
- 本机已确认并修复一个真实 first-run bug：`INCLUDE_SOURCES` 缺省值应为 `''` 而不是 `None`

这几个事实合起来，足够支持前面的总判断：

> **这是个值得继续用的项目，但最好放在正确的位置上。**
