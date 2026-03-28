---
title: baoyu-skills 仓库速览
date: 2026-03-28
tags:
  - ai
  - skills
  - claude-code
  - openclaw
  - toolchain
  - research
---

# baoyu-skills 仓库速览

仓库：<https://github.com/JimLiu/baoyu-skills>
本地路径：`/Users/wangguiping/.openclaw/workspace/external/baoyu-skills`

## TL;DR

这个仓库本质上是一个 **“内容生产 + 发布 + 转换工具”的 skill 大包**，不是通用 agent workflow 仓。

我的判断：

- 它更像是 **内容工作流技能市场 / skill bundle**
- 强项在：内容生成、平台发布、网页/内容转 markdown、图片与文章加工
- 不太像 Anthropic / OpenAI 那种偏“agent harness / 状态管理 / 执行治理”的系统仓

---

## 仓库里大致有哪些东西

### 1. 内容生成类

偏视觉内容生产：

- `baoyu-xhs-images`：小红书图文卡片
- `baoyu-infographic`：信息图
- `baoyu-cover-image`：文章封面图
- `baoyu-slide-deck`：幻灯片
- `baoyu-comic`：知识漫画
- `baoyu-article-illustrator`：文章配图

### 2. 发布分发类

偏发内容到平台：

- `baoyu-post-to-x`
- `baoyu-post-to-wechat`
- `baoyu-post-to-weibo`

### 3. 抓取 / 转换类

这类对当前方向更有参考价值：

- `baoyu-url-to-markdown`：URL 转 markdown
- `baoyu-danger-x-to-markdown`：X / Twitter 转 markdown
- `baoyu-youtube-transcript`：YouTube 字幕 / 稿子
- `baoyu-format-markdown`：整理 markdown
- `baoyu-markdown-to-html`：markdown 转 HTML
- `baoyu-translate`：翻译
- `baoyu-compress-image`：压图

### 4. AI 生成 / 底层能力类

偏模型/浏览器/抓取能力：

- `baoyu-imagine`：多后端图片生成
- `baoyu-danger-gemini-web`
- `packages/baoyu-fetch`
- `packages/baoyu-chrome-cdp`
- `packages/baoyu-md`

---

## 我对这个仓库的判断

### 值得优先关注的

我认为最值得优先看的有这几个：

#### 1）`baoyu-url-to-markdown`

原因：

- 比单纯 `defuddle` 更进一步
- 走 Chrome CDP + 站点适配
- 对登录态、CAPTCHA、动态页面更友好
- 更接近“网页 → markdown → 沉淀”这一条工作流

#### 2）`baoyu-format-markdown`

原因：

- 它不是单纯格式化，而是一个小工作流：分析 → 格式化 → typography 修正
- 对“整理文章/笔记/沉淀内容”这个方向有实际参考价值

#### 3）`baoyu-danger-x-to-markdown`

原因：

- 平时读 X 内容很多
- 这个 skill 对社媒内容沉淀很直接

#### 4）`baoyu-post-to-wechat`

原因：

- 如果以后想把“内容生产 → 发布”工作流串起来，这个值得研究
- 尤其是公众号方向

---

## 当前不算主线的部分

这些更像内容创作工作室能力，当前不是主线：

- `baoyu-comic`
- `baoyu-cover-image`
- `baoyu-infographic`
- `baoyu-slide-deck`
- `baoyu-xhs-images`

不是没用，而是与当前更关心的：

- Agent workflow
- mailbox
- OpenClaw 协作规则
- 状态管理
- 验收闭环

不是同一条主线。

---

## 这个仓库最值得借鉴的 3 个点

### 1. skill 写得很产品化

不是只有命令说明，而是：

- 讲清场景
- 讲清参数
- 讲清默认行为
- 讲清首次配置
- 讲清扩展点

这比很多“只给一个脚本入口”的 skill 成熟得多。

### 2. 很重视 `EXTEND.md`

也就是：

- skill 本体放共享逻辑
- 用户自己的偏好 / 项目特定配置放到扩展层

这个分层思路很好，值得借鉴。

### 3. 它在封装“工作流”，不只是封装“工具”

尤其 `baoyu-url-to-markdown`、`baoyu-format-markdown` 这类 skill，已经不只是 CLI 包装，而是在封装：

- 先做什么
- 失败了怎么办
- 用户什么时候需要介入
- 结果怎么保存

这点是最有价值的。

---

## 一句话总结

`baoyu-skills` 不是通用 agent operating system，而是一个 **高完成度的内容工作流 skill 集合**。

对当前最有参考价值的，不是那些发图发帖的内容生产 skill，而是：

- `baoyu-url-to-markdown`
- `baoyu-format-markdown`
- `baoyu-danger-x-to-markdown`

它们更接近当前最关心的：

> 网页 / 社媒内容抓取 → markdown 化 → 结构化整理 → 知识沉淀
