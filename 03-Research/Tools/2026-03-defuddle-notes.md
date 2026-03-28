---
title: defuddle 记录
date: 2026-03-28
tags:
  - tools
  - defuddle
  - markdown
  - web
  - openclaw
  - research
---

# defuddle 记录

## TL;DR

`defuddle` 是一个把网页提取成 **干净 Markdown** 的工具。

它的定位很明确：

- 输入：普通网页 URL
- 输出：去掉导航、广告、杂讯后的正文内容
- 形式：优先输出 Markdown

对 OpenClaw / Agent 的意义：

- 当用户给一个普通网页链接，想让我“读一下 / 提炼一下 / 分析一下”时
- 不应该直接啃原始网页
- 而应该优先先把网页清洗成 Markdown，再进入阅读和总结流程

---

## 是干什么的

`defuddle` 的核心用途是：

> Extract clean markdown content from web pages.

也就是：

- 从网页里提取主内容
- 去掉导航、边栏、广告、页脚等无关结构
- 输出更适合 AI 阅读和后续处理的 Markdown

它更像一个：

- **网页正文提取器**
- **网页转 Markdown 清洗器**
- **面向 LLM 的降噪层**

---

## 对应 skill

当前 OpenClaw 里有专门的 skill：

- skill 名称：`defuddle`
- 路径：`~/.openclaw/skills/defuddle/SKILL.md`

skill 描述大意：

- 当用户提供 URL 要求阅读、分析、总结网页内容时
- 优先用 Defuddle CLI 提取干净 Markdown
- 用它替代直接读取网页原始内容

---

## GitHub / 安装线索

当前 skill 里明确写到：

- 如果未安装：

```bash
npm install -g defuddle
```

这说明它本体是一个 CLI。

### 安装命令

```bash
npm install -g defuddle
```

---

## 核心用法

### 1. 把网页转成 Markdown

```bash
defuddle parse <url> --md
```

这是默认应该记住的主命令。

### 2. 输出到文件

```bash
defuddle parse <url> --md -o content.md
```

适合后续继续入库、归档、总结。

### 3. 只提取元信息

```bash
defuddle parse <url> -p title
defuddle parse <url> -p description
defuddle parse <url> -p domain
```

适合只想快速确认网页信息，而不是读取全文。

---

## 输出格式

skill 中说明的格式如下：

- `--md` → Markdown（默认优先选项）
- `--json` → JSON（同时包含 HTML 和 Markdown）
- 不加参数 → HTML
- `-p <name>` → 只取某个元信息字段

我的判断：

- 对 AI/Agent 场景，`--md` 是最常用也最划算的
- `--json` 更适合程序化后处理
- HTML 基本不是默认好选择

---

## 适合什么场景

### 1. 读普通网页

比如：

- 博客文章
- 产品介绍页
- 文档页
- 新闻页
- 在线教程

### 2. 给 AI 降噪

网页原始内容往往包含大量：

- 导航栏
- 推荐内容
- 页脚
- 广告
- 评论区
- 多余链接

这些内容对理解正文帮助不大，反而浪费 token。

`defuddle` 的价值就是先做一次清洗。

### 3. 作为知识沉淀前置步骤

如果要把网页内容整理进：

- Obsidian
- knowledge-vault
- 飞书文档
- 总结报告

先过一遍 `defuddle`，内容会干净很多。

---

## 不适合什么场景

### 1. 需要交互的网站

`defuddle` 不是浏览器自动化工具。

所以如果场景是：

- 登录
- 点击按钮
- 翻页
- 填表
- 拉取需要 JS 交互后才出现的数据

它并不适合。

这类场景更适合：

- `opencli`
- `agent-browser`
- Playwright 一类工具

### 2. 需要真实页面行为的网站

比如：

- 复杂前端应用
- 强依赖客户端渲染
- 需要登录态的站点
- 带无限滚动和动态加载的页面

`defuddle` 更偏静态提取，不是完整浏览器执行环境。

---

## 和其他工具的关系

### 和 opencli 的区别

- `opencli`：偏“网站 CLI 化 + 浏览器登录态复用 + 可操作”
- `defuddle`：偏“网页正文抽取 + Markdown 化 + 降噪”

一句话：

- `opencli` 是“能操作网站”
- `defuddle` 是“能把网页正文读干净”

### 和 summarize 的区别

- `defuddle`：负责把网页转成更干净的输入
- `summarize`：负责对内容做总结

也就是说：

- `defuddle` 更像前处理
- `summarize` 更像后处理

### 和 agent-reach / tavily 的区别

- `agent-reach` / `tavily`：偏搜索 / 找内容
- `defuddle`：偏把某个已知 URL 清洗成可读 Markdown

---

## 使用判断

后续遇到下面这些请求时，优先想到 `defuddle`：

- “读一下这个链接”
- “帮我看看这篇文章写了什么”
- “把这个网页内容整理成 markdown”
- “提取一下这个网页正文”
- “这个在线文档帮我读一下”

如果需求变成：

- 登录网站
- 操作网页
- 点按钮
- 搜站内内容

那就不该继续用 `defuddle`，而应该切换到浏览器自动化或 opencli。

---

## 一句话总结

`defuddle` 本质上是一个：

> 给 AI 用的网页清洗器，把普通网页先转成干净 Markdown，再做阅读、总结、沉淀。
