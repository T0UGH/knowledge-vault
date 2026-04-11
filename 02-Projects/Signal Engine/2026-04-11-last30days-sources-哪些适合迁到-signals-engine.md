---
title: last30days-skill 扩展源里，哪些适合迁到 signals-engine
date: 2026-04-11
tags:
  - last30days-skill
  - signals-engine
  - source-inventory
  - lane
  - obsidian
status: done
source_files:
  - /Users/haha/workspace/last30days-skill/README.md
  - /Users/haha/workspace/last30days-skill/scripts/lib/pipeline.py
  - /Users/haha/workspace/last30days-skill/scripts/lib/hackernews.py
  - /Users/haha/workspace/last30days-skill/scripts/lib/polymarket.py
  - /Users/haha/workspace/last30days-skill/scripts/lib/reddit_public.py
  - /Users/haha/workspace/last30days-skill/scripts/lib/youtube_yt.py
  - /Users/haha/workspace/last30days-skill/scripts/lib/bluesky.py
  - /Users/haha/workspace/last30days-skill/scripts/lib/threads.py
  - /Users/haha/workspace/last30days-skill/scripts/lib/xiaohongshu_api.py
  - /Users/haha/workspace/last30days-skill/scripts/lib/grounding.py
  - /Users/haha/workspace/signals-engine/src/signals_engine/sources/
  - /Users/haha/workspace/signals-engine/src/signals_engine/lanes/
related_notes:
  - 03-Research/Claude Code/2026-04-last30days-skill-项目探索调研报告.md
  - 03-Research/Claude Code/2026-04-last30days-skill-快速阅读-哪些设计值得抄到lane上.md
  - 03-Research/Claude Code/2026-04-last30days-skill-对lane第一波可借鉴项讨论备忘.md
  - 02-Projects/Signal Engine/2026-04-08-Codex日报试版与github-watch改造线索.md
---

# last30days-skill 扩展源里，哪些适合迁到 signals-engine

## 这页回答什么

今天重新把 `last30days-skill` clone 到本机后，顺手把它现成 source inventory 和 `signals-engine` 当前 source inventory 对了一遍，想回答一个更工程化的问题：

> **`last30days-skill` 现有扩展源里，哪些值得迁到 `signals-engine`，哪些不该优先迁？**

这里的判断标准不是“这个源酷不酷”，而是：

1. 它能不能稳定地作为 **collector/source 层** 存在；
2. 它是不是和 `signals-engine` 当前的 **lane / signals / index / status** 模型兼容；
3. 它会不会把 `signals-engine` 从稳定收集系统带偏成 topic research engine；
4. 对当前 AI coding / GitHub / repo watch / 日报体系来说，信号密度是否值得投入。

先给结论版：

> **最值得迁的不是最花哨的源，而是最能作为“稳定信号层”存在的源。**
>
> **优先级最高的是：Hacker News、Polymarket、Reddit public、YouTube。**
>
> **Bluesky 可以做第二波；Threads / TikTok / Instagram / Pinterest / 小红书都更像传播面增强器，不适合先做主链。**
>
> **Grounding / Perplexity 这类 web / AI search 更适合留在研究层，不适合优先进入 collect 主链。**

---

## 1. 两边当前各有什么

## 1.1 `signals-engine` 当前已有 source

当前 `signals-engine/src/signals_engine/sources/` 里已经有的主要源是：

- `x`
- `github`
- `github_trending`
- `producthunt`

也就是说，当前 `signals-engine` 还是一个比较明确的：

> **timeline / GitHub / 产品榜单型信号系统**

它还不是一个多源 topic research engine。

## 1.2 `last30days-skill` 现成可借的 source

`last30days-skill/scripts/lib/` 里已经有的 source 级模块包括：

- `reddit_public.py`
- `reddit.py`
- `youtube_yt.py`
- `github.py`
- `hackernews.py`
- `polymarket.py`
- `bluesky.py`
- `threads.py`
- `tiktok.py`
- `instagram.py`
- `pinterest.py`
- `xiaohongshu_api.py`
- `truthsocial.py`
- `grounding.py`
- `perplexity.py`
- `bird_x.py`
- `xquik.py`

而 `pipeline.available_sources()` 当前能动态启用的源包括：

- Reddit
- TikTok
- Instagram
- X
- YouTube
- GitHub
- Bluesky
- Truth Social
- Polymarket
- grounding / web
- Perplexity
- 小红书
- Threads
- Pinterest
- Xquik

这说明 `last30days-skill` 在 source 覆盖面上已经很宽，但宽不等于都应该迁进 `signals-engine`。

---

## 2. 判断原则：什么叫“适合迁到 signals-engine”

要迁到 `signals-engine` 的 source，优先满足下面几条：

### 2.1 稳定 collector 优先于 research helper

如果一个源更像：

- 稳定列表/流
- 结构化信号
- 可做 state / seen / dedupe
- 可天然产出 signal records

那它更适合 `signals-engine`。

如果一个源更像：

- topic 搜索补充
- web grounding
- 需要大模型判断 relevance
- 结果随 query 漂移很大

那它更适合继续留在 `last30days` 这种 research engine 里。

### 2.2 source 适配复杂度不要压过 signal 价值

如果一个源要：

- 登录态复杂
- 外部第三方代理依赖强
- API 容易漂移
- 平台对 coding / devtools 信号密度又一般

那它就不适合优先进主链。

### 2.3 优先补“当前日报盲区”

当前 `signals-engine` 已经有：

- GitHub 工程面
- X 时间线面
- Product Hunt / Trending 面

所以最值得补的是它现在缺的：

- 开发者社区讨论面
- 真实用户评论面
- 长视频解释面
- 非内容型概率/预期面

---

## 3. 第一梯队：最适合迁的 source

## 3.1 Hacker News

对应文件：`scripts/lib/hackernews.py`

### 为什么适合

这个模块的特点非常像理想中的 `signals-engine` source：

- 用 Algolia API
- 免费
- 无鉴权
- 结构清楚
- 已经有 date range / depth / story + comment enrichment
- 完全是标准 HTTP，不依赖浏览器 session

### 为什么它信号价值高

对于 AI coding / devtools / infra / open-source 生态，HN 依然是高密度讨论面：

- 新工具发布后的开发者反应
- 架构/安全/性能争论
- 对产品定位的技术社区判断
- 相比 X 更长、更成体系的评论结构

### 适合的 lane 形态

- `hn-watch`
- `hn-topic-watch`
- `hn-domain-watch`
- `hn-tool-watch`

### 结论

> **HN 是最应该优先迁的非现有 source。**

---

## 3.2 Polymarket

对应文件：`scripts/lib/polymarket.py`

### 为什么适合

它和 HN 一样，满足很多 collector 层偏好：

- public API
- 免费
- 无鉴权
- 结构化结果强
- 有 query expansion / filtering / result cap

### 为什么它有独特价值

Polymarket 和普通内容源不一样，它不是“谁说了什么”，而是：

> **市场在押什么，概率往哪边走。**

这能给日报体系补一种当前很缺的 signal：

- 预期
- 概率变化
- 热点是否被真钱验证

### 适合的 lane

- `polymarket-watch`
- `prediction-market-watch`
- 某主题的 contract watch

### 结论

> **Polymarket 值得优先做，因为它补的是一类与 GitHub/X 完全不同的信号维度。**

---

## 3.3 Reddit public

对应文件：`scripts/lib/reddit_public.py`

### 为什么适合

这个模块比 `reddit.py`（ScrapeCreators 版）更适合 `signals-engine` 的主链：

- 无 API key
- 有 429 backoff
- 有 anti-bot HTML handling
- 有 subreddit/global search 两类入口
- 可以补 comments enrichment

### 为什么它有价值

Reddit 对工具、开源、agent workflow 的真实使用反馈很重要，尤其适合抓：

- 上手痛点
- 争议点
- 替代品对比
- “真实用户怎么吐槽/推荐”

这恰好是 GitHub release / PR 看不到的部分。

### 为什么先做 public 版

如果第一波就搬 `ScrapeCreators Reddit`，会把 `signals-engine` 更深地绑到外部付费接入层。

更稳的路径是：

1. 先做 `reddit_public`
2. 跑通 lane / signal / status 模型
3. 再考虑是否加 richer Reddit adapters

### 适合 lane

- `reddit-watch`
- `subreddit-watch`
- `reddit-topic-watch`

### 结论

> **Reddit public 很值得优先进入 `signals-engine`，因为它便宜、稳、讨论密度高，而且不需要把主链先绑到第三方付费 API。**

---

## 3.4 YouTube

对应文件：`scripts/lib/youtube_yt.py`

### 为什么它值得迁

YouTube 对 coding / tool / AI workflow 跟踪的价值在于：

- 长视频解释面
- founder / creator / reviewer 视角
- 对新工具的 walkthrough / benchmark / live reaction
- 可以补 transcript / highlights

### 为什么它不像 HN/Reddit 那么简单

YouTube 模块更重：

- 依赖 `yt-dlp`
- transcript 提取比较重
- 搜索/字幕/ highlights 链路更复杂
- 失败模式比纯 HTTP API 多

### 正确迁法

不建议第一步直接迁完整 transcript intelligence。

第一波更合理的是：

1. 先迁 search metadata
2. 保留 title / channel / date / url / snippet
3. transcript 先做可选 enrich
4. highlights 留到第二阶段

### 适合 lane

- `youtube-watch`
- `channel-watch`
- `youtube-topic-watch`

### 结论

> **YouTube 值得做，但应该用“metadata-first, transcript-later”的策略迁。**

---

## 4. 第二梯队：可以迁，但不该压过第一梯队

## 4.1 Bluesky

对应文件：`scripts/lib/bluesky.py`

### 优点

- 协议相对干净（AT Protocol）
- 需要 app password，但不是那种极度脆弱的网页 session 抓取
- 对开发者 / 研究者迁移后的讨论面有价值

### 为什么不是第一梯队

因为现在对你当前主任务来说，Bluesky 的信号密度通常还不如：

- HN
- Reddit
- GitHub
- YouTube

### 结论

> **如果要补“后 X 时代”的开发者讨论面，Bluesky 是第二波里最值得做的。**

---

## 4.2 Threads

对应文件：`scripts/lib/threads.py`

### 优点

- 短文本社交面
- 有 engagement 指标
- 结构上不难抽象成 source

### 缺点

- 依赖 `SCRAPECREATORS_API_KEY`
- 平台对当前 AI coding 生态的真实高信号密度一般不如 HN / Reddit / GitHub

### 结论

> **Threads 可以做，但更像传播面补充，不该早于 HN / Reddit / YouTube / Polymarket。**

---

## 4.3 TikTok / Instagram / Pinterest

对应文件：

- `scripts/lib/tiktok.py`
- `scripts/lib/instagram.py`
- `scripts/lib/pinterest.py`

### 为什么先放后面

它们都更偏：

- 传播面
- 视觉/短视频面
- creator economy 面
- 高度依赖 `SCRAPECREATORS_API_KEY`

这些源不是没价值，但更适合回答：

- 这个产品在 creator 圈有没有扩散
- 这个话题在更大众内容层怎么被演绎

而不是当前 `signals-engine` 更关心的：

- 工程变化
- 开发者争议
- repo 更新
- 技术工作流判断

### 结论

> **TikTok / Instagram / Pinterest 更像传播面增强器，不适合先做主 collect 链。**

---

## 4.4 小红书

对应文件：`scripts/lib/xiaohongshu_api.py`

### 为什么不优先

它的接入明显更脆：

- 依赖外部 REST API
- 需要 login status
- 可移植性不如 HN / Reddit / YouTube
- 更像“环境特定能力”而不是通用 source

### 什么时候值得做

如果后续重点转向：

- 中文 AI 工具传播
- 开发者工具中文社区扩散
- 内容平台上的工具口碑跟踪

那小红书非常值得单独成 lane。

### 结论

> **小红书不是没价值，而是不适合在 `signals-engine` 还很 lean 的时候先做成主线 source。**

---

## 5. 第三梯队：不建议优先迁

## 5.1 Grounding / Web search

对应文件：`scripts/lib/grounding.py`

### 为什么不适合优先做 collector

web grounding 对 `last30days` 很重要，因为它是 research engine，需要补 editor/web coverage。

但对 `signals-engine` 来说，web search 更像：

- enrichment
- fallback
- briefing 引用补充
- topic research sidecar

而不是稳定 collector。

原因在于：

- 搜索结果漂移大
- 排序由外部搜索引擎控制
- query 依赖强
- 很容易把系统带向 topic-search，而不是 lane-watch

### 结论

> **Web search 更适合留在研究层，不适合先搬进 collect 主链。**

---

## 5.2 Perplexity

对应文件：`scripts/lib/perplexity.py`

### 为什么更不该先进 collect 层

它本质上是：

- AI search / grounded synthesis source
- 成本更高
- 输出更不稳定
- 更偏 reasoning layer

这和 `signals-engine` 当前“稳定 source -> signal -> index -> report”的 collect 心智是相反的。

### 结论

> **Perplexity 适合作为研究增强器，不适合作为优先 collector source。**

---

## 5.3 Truth Social / Xquik / Bird-X 替代路径

这些源/路径更多是 `last30days` 为覆盖更广 topic research 面做的适配层。

对 `signals-engine` 来说：

- Truth Social 与当前 coding-agent 生态相关性弱
- Xquik / Bird-X 是 X search 的替代/补充，不该早于更大的 source 空白（HN/Reddit/YouTube）

### 结论

> **它们可以保留为 future options，但都不该进入最近的 source migration 队列。**

---

## 6. 建议的 source migration 优先级

如果只从工程投入和信号收益比看，我会给出这个顺序：

## 第一波

1. **Hacker News**
2. **Reddit public**
3. **YouTube（metadata-first）**
4. **Polymarket**

## 第二波

5. **Bluesky**
6. **Threads**

## 第三波

7. **TikTok / Instagram / Pinterest / 小红书**

## 不建议放进近期主线

8. **Grounding / Perplexity / Truth Social / Xquik / Bird-X 平行路径**

---

## 7. 真正该迁的是 lane 能力，不只是 source 文件

最后要特别记一条：

> **不应该按“把 source 文件抄过来”来理解迁移，而应该按“哪条 lane 缺哪种观察面”来理解迁移。**

也就是：

- 不是先问“要不要搬 `hackernews.py`”
- 而是先问“我们要不要做 `hn-watch` 这种 lane”

迁移正确姿势应该是：

1. 先定 lane 形态
2. 再定 source adapter
3. 再定 signal schema
4. 再定 state / status / run.json / index
5. 最后才是 editor/report 怎么消费

这样才能保证 `signals-engine` 还是：

> **稳定、可解释、可复放的信号系统**

而不是逐步变成一个 topic research 大杂烩。

---

## 8. 当前最推荐的下一步

如果明天继续往前推进，最值得做的不是“再聊一轮 source 想法”，而是直接出一版 implementation sketch：

### 推荐先做

1. `hn-watch` lane
2. `reddit-watch` lane（public-first）
3. `youtube-watch` lane（metadata-first）

### 推荐先不做

- web grounding lane
- Perplexity lane
- 把 topic research planner 直接塞进 `signals-engine`

一句话落版：

> **`last30days-skill` 里最值得迁进 `signals-engine` 的扩展源，是那些本身就更像“稳定 collector”的源：HN、Polymarket、Reddit public、YouTube；而不是那些更像 research 辅助或传播面补充的源。**
