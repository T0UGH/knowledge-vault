---
title: 日报转朗读稿预处理 Pipeline 规格
date: 2026-04-15
tags:
  - daily-report
  - tts
  - preprocessing
  - pipeline
status: draft
related:
  - 2026-04-15-minimax-tts-daily-report-integration-plan.md
---

# 日报转朗读稿预处理 Pipeline 规格

> 目标：把现有 AI 日报 markdown 稳定转换成一个适合 TTS 朗读的脚本文本（readout script），供 MiniMax 等 TTS 服务直接消费。

---

## 1. Pipeline 定义

### 输入
- 一篇日报 markdown 文件
- 可选 metadata：
  - 日期
  - 主标题
  - 来源路径
  - 目标时长（可选）

### 输出
- 一个 `script.txt` 或 `script.md` 风格的纯朗读稿
- 一个 `script.json` 中间产物，记录结构化段落与清洗结果

### 最终目标
输出的文本应满足：

1. **可以直接交给 TTS 朗读**
2. **听起来像晨报播报，不像文档朗读**
3. **保留原日报的关键信息与具体更新点**
4. **删除对音频无意义的 markdown / 链接 / 调试噪音**

---

## 2. 总体流程

```text
read markdown
-> parse structure
-> normalize blocks
-> strip non-speakable content
-> classify sections/items
-> rewrite into spoken Chinese
-> inject transitions/opening/closing
-> validate script
-> emit script + metadata
```

对应成 8 个明确步骤：

1. **读取与解析**
2. **结构归一化**
3. **删噪音**
4. **分栏目标记**
5. **条目口播改写**
6. **注入过渡句**
7. **全稿检查**
8. **输出结果**

---

## 3. 输入格式约束

假设原日报大体长这样：

```md
# AI 日报（2026-04-15）

## Claude Code
- **v2.1.108** ...
- **v2.1.107** ...

## Codex
- ...

## 来源
- ...
```

### 可接受元素
- H1 主标题
- H2 section 标题
- bullet 列表
- 少量段落文字
- 加粗文本
- markdown 链接

### 不鼓励但必须兼容的元素
- 表格
- code block
- 嵌套 bullet
- 多行 URL
- 中英混杂 section

---

## 4. 中间数据结构

建议在真正生成朗读稿前，先转成结构化 JSON。

### `ReportDoc`

```json
{
  "title": "AI 日报（2026-04-15）",
  "date": "2026-04-15",
  "sections": [
    {
      "raw_title": "Claude Code",
      "kind": "product_updates",
      "items": [
        {
          "raw_text": "**v2.1.108** ...",
          "source_type": "github_release",
          "clean_text": "v2.1.108 ...",
          "spoken_text": "Claude Code 发布了 2.1.108 ..."
        }
      ]
    }
  ]
}
```

### 为什么先转 JSON
因为这样：
- 规则更好测试
- 以后更容易做 chunk
- 以后更容易切换 TTS 厂商
- 以后更容易加时长预算与栏目裁剪

---

## 5. Step-by-step 规格

## Step 1：读取与解析

### 输入
- `report.md`

### 任务
- 读取 UTF-8 文本
- 抽出主标题
- 按 `## ` 分 section
- 提取每个 section 下的 bullet 和普通段落

### 输出
- 初步的 `ReportDoc`

### 规则
- H1 作为主标题
- H2 作为 section 标题
- `- ` / `* ` 开头视为 bullet
- `## 来源` / `## Sources` 单独标成 `sources_section`

### 失败处理
- 没有 H1：允许继续，但 title 置空
- 没有 H2：把全文作为一个默认 section

---

## Step 2：结构归一化

### 任务
把各种 markdown 元素归一成统一 block：

- `heading`
- `paragraph`
- `bullet`
- `table`
- `code`
- `linklist`

### 规则
- 连续 bullet 合并成一个逻辑列表
- 多行 bullet 合成一条 item
- 加粗标记去掉，但保留内容
- markdown 链接 `[text](url)` 保留 text，临时挂住 url

### 输出
每个 item 至少有：
- `raw_text`
- `plain_text`
- `urls[]`
- `format_flags`

---

## Step 3：删噪音

### 必删内容
以下内容默认不进入朗读稿：

1. `Sources` / `来源` 整段链接清单
2. code block
3. markdown table
4. 裸 URL
5. 文件路径
6. 调试产物路径
7. 只对视觉阅读有意义的结构噪音

### 保留但重写的内容
- `[GitHub](...)` -> `来源是 GitHub`
- `[原帖](...)` -> `来源是 X 原帖`
- `[Release](...)` -> `来源是发布说明`

### 输出
- `clean_text`
- `source_hints[]`

---

## Step 4：分栏目标记

### 任务
把 section 归类，决定它后面用什么口播模板。

### 推荐分类
- `opening`
- `top_picks`
- `github_release`
- `x_posts`
- `reddit_posts`
- `product_hunt`
- `prediction_market`
- `github_trending`
- `closing`
- `sources_section`
- `unknown`

### 归类规则（第一版可 rule-based）

#### 根据 section title 映射
- `Claude Code` -> `github_release`
- `Codex` -> `github_release`
- `OpenClaw` -> `github_release`
- `X 推荐流` / `X 关注流` -> `x_posts`
- `Reddit 社区` -> `reddit_posts`
- `GitHub 趋势项目` -> `github_trending`
- `Product Hunt 新品` -> `product_hunt`
- `Polymarket 市场` -> `prediction_market`
- `来源` / `Sources` -> `sources_section`

### 输出
每个 section 都带：
- `kind`
- `spoken_heading`

示例：

```json
{
  "raw_title": "GitHub 趋势项目",
  "kind": "github_trending",
  "spoken_heading": "再看今天值得注意的 GitHub 趋势项目。"
}
```

---

## Step 5：条目口播改写

这是最核心的一步。

### 统一目标
每条 item 改写成 **2~3 句中文口播句**。

### 统一模板
每条尽量回答三件事：
1. 这是什么
2. 具体变了什么
3. 为什么值得听

### 各类 item 的模板

#### A. GitHub release / changelog
输入示例：
- `v2.1.108 发布，新增 away summary 与 prompt cache TTL 配置。`

输出模板：
```text
Claude Code 发布了 2.1.108。这版最值得记的是……另外还有……对你最相关的影响是……
```

#### B. X 帖子
输出模板：
```text
X 上有一条值得收的帖子，来自某某。它真正值得听的点是……具体来说……
```

#### C. Reddit
输出模板：
```text
Reddit 上有一条长讨论值得收。它的核心不是情绪，而是把……讲透了。里面最值得记的有两点……
```

#### D. Product Hunt
输出模板：
```text
Product Hunt 上今天有个值得提的新产品，叫某某。它的定位是……之所以值得放进日报，是因为……
```

#### E. Polymarket
输出模板：
```text
Polymarket 这边，今天有一条值得看的市场是……当前领先的是……概率大概在……这条市场值得听，是因为……
```

### 内容压缩规则
如果一条内容太长：
- 最多保留 2~3 个具体点
- 删除过细 patch list
- 删除重复修辞
- 删除空话

### 空话黑名单
这些句式直接禁用或降级：
- `值得关注`
- `进一步说明了`
- `原文围绕……展开`
- `具体变化见来源`
- `体现了趋势`
- `更清楚了`

---

## Step 6：注入过渡句

### 开场
固定模板先用一条：

```text
以下是今天的 AI 日报，我挑几条最值得听的内容给你过一遍。
```

### section 过渡句
每个 section 前插入一条过渡句。

#### 示例
- `先看 Claude Code。`
- `再看 Codex。`
- `接着看 OpenClaw。`
- `再看今天值得注意的 GitHub 趋势项目。`
- `最后补一条市场侧信号。`

### 收尾
固定模板：

```text
以上就是今天这份 AI 日报。如果你愿意，下一步还可以把它压缩成更短的晨间播报版。
```

---

## Step 7：全稿检查

生成完整朗读稿后，做一轮 rule-based 验证。

### 必须通过的检查

#### 检查 1：不能出现裸 URL
- 命中 `http://` 或 `https://` 直接报错

#### 检查 2：不能保留 `Sources:` 大段
- 命中 `Sources:` / `## 来源` 直接报错

#### 检查 3：不能出现 markdown 结构噪音
- `# `
- `## `
- `` ``` ``
- `| --- |`

#### 检查 4：句长不能连续过长
- 连续 2 句超过阈值（如 60 汉字）则警告

#### 检查 5：英文密度不能过高
- 单句里英文 token 过多则警告

#### 检查 6：至少有开场和收尾
- 缺失则报错

---

## Step 8：输出结果

### 输出 1：`script.txt`
纯文本，供 TTS 直接读。

### 输出 2：`script.json`
结构化元数据，供后续 chunk / 调试。

示例：

```json
{
  "title": "AI 日报（2026-04-15）",
  "script_path": "outputs/readout/2026-04-15.txt",
  "section_count": 7,
  "item_count": 18,
  "warnings": [
    "section Codex 英文名偏多"
  ]
}
```

---

## 6. 规则执行顺序（必须固定）

顺序不要乱，建议严格按下面执行：

1. parse markdown
2. flatten bullets/paragraphs
3. strip code/table/urls/sources
4. classify sections
5. rewrite items into spoken Chinese
6. inject transitions
7. validate script
8. emit outputs

原因：
- 先删噪音，再改写，避免把垃圾也改写进朗读稿
- 先分类，再套模板，避免 section 风格混乱
- 先生成全文，再统一校验，避免局部过关整体翻车

---

## 7. 最小伪代码

```python
def preprocess_report_to_script(markdown_text: str) -> dict:
    doc = parse_markdown(markdown_text)
    doc = normalize_blocks(doc)
    doc = strip_non_speakable(doc)
    doc = classify_sections(doc)

    for section in doc.sections:
        if section.kind == "sources_section":
            continue
        section.spoken_heading = build_spoken_heading(section)
        for item in section.items:
            item.spoken_text = rewrite_item(section.kind, item.clean_text, item.source_hints)

    script = compose_script(doc)
    validation = validate_script(script)
    return {
        "doc": doc,
        "script": script,
        "validation": validation,
    }
```

---

## 8. 第一版建议哪些地方不要上 LLM

这些地方第一版最好 rule-based：
- markdown 解析
- 删除 links / code / tables / sources
- section 分类
- 数字/日期/百分比转换
- 开场/收尾/过渡句注入
- 基础校验

## 哪些地方后续可以引入 LLM
- 单条内容的口播重写
- 重点压缩
- 句子自然化
- 中英混读优化

我的建议是：

### V1
- 80% rule-based
- 20% 模板改写

### V2
- 再把 `rewrite_item()` 升级成 LLM-assisted

这样最稳。

---

## 9. 验收标准

只要满足以下标准，就算第一版成功：

1. 给一篇现有日报，能稳定生成一份纯文本朗读稿
2. 朗读稿里没有 markdown 噪音、裸链接、Sources 清单
3. 听起来像晨报播报，不像文档朗读
4. 每条仍保留具体更新点，不被抽象空话洗掉
5. 能无缝接到 MiniMax TTS

---

## 10. 推荐下一步

下一步最合适的是实现一个最小 CLI：

```bash
python scripts/report_to_readout.py input.md --out script.txt --meta script.json
```

第一版只要跑通这条命令，就已经能接后面的 chunk + TTS + merge。
