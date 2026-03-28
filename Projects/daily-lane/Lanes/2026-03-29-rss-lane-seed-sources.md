---
title: RSS Lane Seed Sources
date: 2026-03-29
tags:
  - daily-lane
  - rss
  - newsletter
  - source-list
status: draft
source_repo: https://github.com/T0UGH/daily-lane
---

# RSS Lane Seed Sources

这份笔记用于给 `RSS / Newsletter lane` 的 Phase 1 提供第一批可接入 source seed。

目标不是一次性列全，而是先找出：

- **和贵平当前观察面高度相关**
- **有稳定 RSS/Atom feed**
- **适合进入日报与知识/写作 pipeline**

的第一批可用源。

---

## 一、推荐直接进入第一批白名单的源（已验证 feed 可用）

> 说明：以下 feed 地址均已在本机实际请求验证过，可作为第一版 lane 的候选输入源。

| Source | Homepage | Feed URL | Category | Why it matters |
|---|---|---|---|---|
| Simon Willison | https://simonwillison.net/ | https://simonwillison.net/atom/everything/ | ai-tools / llm / agent | 高密度、强实践、非常适合待学习与写作素材池 |
| One Useful Thing | https://www.oneusefulthing.org/ | https://www.oneusefulthing.org/feed | ai-usage / org / education | 适合公众号写作，不只是技术更新 |
| Latent Space | https://www.latent.space/ | https://www.latent.space/feed | ai-engineering / agent / tooling | 和 Claude Code / coding agent / dev workflow 生态贴近 |
| Interconnects | https://www.interconnects.ai/ | https://www.interconnects.ai/feed | models / open-weight / research | 适合中深度技术判断与长期跟踪 |
| OpenAI News | https://openai.com/news/ | https://openai.com/news/rss.xml | model-vendor / official | 日报主干源之一，官方高优先级 |
| GitHub Blog | https://github.blog/ | https://github.blog/feed/ | developer-platform / official | 对代码工作流、平台变化、AI tooling 很关键 |
| Cloudflare Blog | https://blog.cloudflare.com/ | https://blog.cloudflare.com/rss/ | infra / engineering | 经常有高质量技术文章，适合写作素材 |
| Vercel Blog | https://vercel.com/blog | https://vercel.com/atom | developer-tools / product | 偏产品化与开发者工具趋势 |
| Chip Huyen | https://huyenchip.com/ | https://huyenchip.com/feed.xml | ai-engineering / ml-systems | 适合知识沉淀与方法论写作 |
| swyx | https://www.swyx.io/ | https://www.swyx.io/rss.xml | ai-engineering / developer-thoughts | 观点型内容多，适合提炼写作题材 |
| Lilian Weng | https://lilianweng.github.io/ | https://lilianweng.github.io/index.xml | research / longform | 高价值低频长文，适合进入待学习清单 |

---

## 二、已验证但建议按优先级分层接入

### A 档：建议第一版就接

这些源最适合直接进入 `RSS lane` 第一批白名单：

1. Simon Willison
2. Latent Space
3. One Useful Thing
4. Interconnects
5. OpenAI News
6. GitHub Blog
7. Chip Huyen

理由：

- 与当前 AI coding / agent / workflow 观察面高度相关
- 既能服务日报，也能服务知识/写作 pipeline
- 内容密度与长期价值都比较高

### B 档：建议作为第一版扩展批

1. Cloudflare Blog
2. Vercel Blog
3. swyx
4. Lilian Weng

理由：

- 价值高，但和当前“日报主干信号”相比，优先级略低一档
- 更偏补充视角、方法论、工程洞察

---

## 三、待确认 / 当前不稳定的源

### Anthropic News

- Homepage: https://www.anthropic.com/news
- 结论：**当前没有在页面上直接发现稳定公开 RSS，且 `https://www.anthropic.com/news/rss.xml` 返回 404**
- 说明：Anthropic 对贵平的观察面非常重要，但它当前看起来不像 OpenAI / GitHub 那样直接公开标准 news feed
- 暂定处理：
  - 不把它当成 RSS lane 第一版的必需源
  - 后续如果需要，可单独做站点/页面抓取型 lane 或为 Anthropic 做特化抓取

### Sebastian Raschka

- Homepage: https://sebastianraschka.com/
- 尝试结果：多个常见 feed 路径（如 `/rss.xml`、`/feed.xml`、`/index.xml`）请求均返回 406
- 当前判断：**站点可能对自动请求较敏感，或需要更特定 header / client 行为**
- 暂定处理：
  - 先不把它放入第一版稳定白名单
  - 若后续值得重点接入，再做专项探测

---

## 四、这批源为什么适合当前系统

当前 `daily-lane` 召回层已经覆盖：

- GitHub repo / release / changelog / README
- X watchlist / feed
- Product Hunt
- GitHub Trending Weekly

但仍相对缺少：

- **稳定订阅源**
- **长文字解释型内容**
- **适合进入知识沉淀与写作素材池的 source**

因此 RSS lane 的价值不在于“再多抓一种网页”，而在于补上：

> **主动订阅型、低算法噪音、适合长期积累的高密度内容层。**

这层既能服务：

- **日报 pipeline**：补充当天值得知道的结构化外部观点
- **知识 / 写作 pipeline**：沉淀待学习文章、形成选题与表达素材

---

## 五、对 Phase 1 的实际建议

如果现在要开始写 `RSS lane` 的 Phase 1 spec，我建议：

### 第一批 source 不超过 7 个

建议从下面 7 个开始：

1. Simon Willison
2. Latent Space
3. One Useful Thing
4. Interconnects
5. OpenAI News
6. GitHub Blog
7. Chip Huyen

### Phase 1 目标

- 先做 **RSS-first**，不扩成“所有 newsletter 入口”
- signal 单元优先收成：**一篇 feed entry = 一条 signal**
- 保留 feed 元信息：source、published_at、title、link、summary 等
- 不在 lane 层做 AI 过滤或推荐判断

---

## 六、一句话结论

截至 2026-03-29，`RSS / Newsletter lane` 的第一批 seed source 已可落地：当前已验证 11 个可用 feed，其中最适合先进入 Phase 1 白名单的是 Simon Willison、Latent Space、One Useful Thing、Interconnects、OpenAI News、GitHub Blog 与 Chip Huyen；Anthropic News 当前未发现公开稳定 RSS，Sebastian Raschka 站点对自动请求较敏感，可留作后续专项接入对象。
