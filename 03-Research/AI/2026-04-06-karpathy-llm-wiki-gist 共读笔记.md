---
title: LLM Wiki 共读笔记
date: 2026-04-06
tags:
  - reading-notes
  - llm-wiki
  - karpathy
  - knowledge-vault
status: active
---

# LLM Wiki 共读笔记

原文：[[2026-04-06-karpathy-llm-wiki-gist]]

## 第 1 轮

### 原文范围
- 开头 + The core idea

### 中文解释 / 提炼
Karpathy 先打掉的是主流 query-time RAG 心智。

他的核心判断不是“RAG 不行”，而是：

- 每次 query 时临时检索、临时拼答案
- 知识不会累积
- 多文档之间的 synthesis、矛盾、交叉链接不会沉淀

他真正想推的是：

> wiki 不是 query 的副产物，而是知识工作的主产物。

也就是让 LLM 持续维护一个 **persistent, compounding artifact**，而不是每次重新从 raw docs 里现找。

### 讨论要点
- 贵平明确倾向让长期知识库走 **agent 主维护** 路线，而不是“agent 帮忙整理，人仍是主要维护者”。
- 这意味着后续方向不是“更好用的笔记系统”，而是“LLM Wiki / agent 主维护知识系统”。

## 第 2 轮

### 原文范围
- You never (or rarely) write the wiki yourself ... Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase.

### 中文解释 / 提炼
这一段重新定义了人和知识库的分工：

- 人负责：sourcing / exploration / asking the right questions
- LLM 负责：summarizing / cross-referencing / filing / bookkeeping

关键不是“LLM 帮我写点笔记”，而是：

> wiki 的主要维护者就是 LLM，人主要负责 source、判断、review。

“Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase” 这句特别重要，因为它把：
- Obsidian 从笔记 app 提升成知识系统的 review 界面
- wiki 从资料堆提升成被持续编译、持续维护的产物

### 讨论要点
- 贵平明确选择：**agent 主维护模式（B）**。
- 当前最关键的转变不是“怎么写更好”，而是“怎么设计知识维护工作流”。
- 结论：贵平不是知识库的主要写作者，而更像知识库维护系统的设计者 / 审稿人 / 研究导演。

## 第 3 轮

### 原文范围
- Architecture（三层：Raw sources / The wiki / The schema）

### 中文解释 / 提炼
Karpathy 的架构拆法：

1. **Raw sources**
   - 原始材料层
   - immutable
   - LLM 只读不改

2. **The wiki**
   - LLM 生成并维护的 markdown wiki
   - 包含 summaries / entities / concepts / comparisons / synthesis
   - 关键句：**You read it; the LLM writes it.**

3. **The schema**
   - 维护协议层
   - 定义 wiki 如何组织、如何 ingest、如何 query、如何 maintain

对贵平当前系统的判断：
- Raw sources：已经有雏形
- Wiki：`knowledge-vault` 已经有雏形
- Schema：**还不够硬**，这是当前最大的缺口

### 讨论要点
- 今天已经拍板的很多规则，其实都是 schema 原材料：
  - 内容创作按主题工作区
  - `公众号创作` / `07-Posts/WeChat` 退场
  - README/index 要补
  - Projects / Research / Reference 分层
  - 自建 skill 真身 / 安装态分离
- 结论：下一阶段不只是“继续整理 vault”，而是要把这些规则提升成 **knowledge-vault 的维护协议**。
- 贵平认为：可以尝试按三层思路推进，但先不急着做“物理三层”大拆目录。

## 第 4 轮

### 原文范围
- Operations（Ingest / Query / Lint）

### 中文解释 / 提炼
Karpathy 这里给出了 LLM Wiki 的运行闭环：

1. **Ingest**
   - 新 source 进入 raw
   - LLM 读取并和人讨论要点
   - 写 summary
   - 更新 index
   - 更新 entity / concept pages
   - 记录 log
   - 一次 ingest 可能触发 10~15 个页面更新

2. **Query**
   - query 不是终点
   - 好的 comparison / analysis / discovered connection 应回写成新的 wiki 页面
   - 也就是说 query 也在生产知识

3. **Lint**
   - 周期性健康检查
   - 看矛盾、过期结论、孤儿页、缺概念页、缺链接、缺 source

### 讨论要点
- 贵平当前系统已经有这三者的雏形：
  - ingest：已有
  - query 回写：已有但不协议化
  - lint：今天做了一整轮人工版
- 关键判断：如果要走 LLM Wiki 路线，真正需要的是把这三件事正式变成 protocol。
- 当前最值得优先收口的，很可能不是 lint，而是：
  - **query-backfill / query 回写 protocol**

## 当前阶段结论

### 1. 路线选择
- 贵平明确选择：**agent 主维护知识库**，不是 agent 辅助整理。

### 2. 结构判断
- 可以按 Raw / Wiki / Schema 三层去理解当前系统
- 但先按**逻辑三层**推进，不急着做**物理三层**目录改造。

### 3. 当前最大缺口
- 不是再多收笔记
- 而是缺一份 **knowledge-vault schema / maintenance protocol**

### 4. 下一步最值得讨论
- ingest protocol
- query-backfill protocol
- lint protocol

当前判断：**query-backfill protocol 可能最缺。**

## 继续贯彻 Karpathy 这套理论时，额外值得推进的 4 个点

### 1. Source 和 Wiki 要更明确地区分
当前 vault 里已经自然长出一些 raw/source 层（如 `00-source/`、外部文章 markdown、gist、本地化的原始资料），但全局上还没有形成清晰的一致规则。

后续需要进一步明确：
- 哪些页面是 raw source
- 哪些页面是 agent 维护后的 wiki page
- LLM 面对某个文件时，是“只读引用”还是“可维护更新”

当前判断：这层还不一定要马上物理建成统一 `raw/` 目录，但逻辑上必须越来越清楚。

### 2. README / index 不只是导航，而是 wiki 的一等页面
当前已经开始在多个高密度目录补 `README.md`，但它们还偏“给人看的入口页”。

Karpathy 那套里，index 不只是导航文件，而是：
- 给 LLM 查询时先读的工作入口
- wiki 结构本身的一部分

后续 README/index 应逐步承担更多职责：
- 当前主题定义
- 最近状态
- 推荐先读什么
- 关键派生页
- 当前 active sources / active questions（视需要）

### 3. Query 不只是回答问题，而要默认考虑“是否回写”
这一点已经开始讨论 protocol，但仍然需要强调：

- query 不只是消费知识
- 高价值讨论默认应考虑是否回写成新的 wiki 资产

典型回写类型：
- decision
- protocol
- analysis
- reading synthesis

这也是当前最缺、最值得先 formalize 的协议方向。

### 4. Schema 要持续演化，不是写完 `VAULT.md` 就结束
`VAULT.md` 只是 vault-level schema 的 v0 总纲，不是终点。

后续应继续把它演化成更清晰的子协议，例如：
- ingest protocol
- query-backfill protocol
- lint protocol
- multi-agent maintenance rules
- source vs wiki distinction rules

当前判断：`VAULT.md` 是第一颗种子，schema 的长期价值在于持续演化，而不是一次写完。
