---
title: Signal Repo Phase 1 Brainstorm
date: 2026-03-28
tags:
  - signal-repo
  - product-architecture
  - github-watch
  - phase1
status: draft
---

# Signal Repo / Phase 1 讨论整理

## 这套系统是什么
当前共识：这不是一个单纯的“日报生成器”或“仓库收藏夹”，而是一个以 **signal 为中心** 的情报系统。

更准确地说，它是一个：

- 以 **lane / route / channel** 为第一公民的多路召回系统
- 底层尽量保留原始 signal 和证据
- 上层再逐步形成候选项、日报项、长期观察对象
- 关键决策点保持 **半自动**，而不是全自动

一句话版本：

> 日报只是下游产物之一；系统本体是“按 lane 组织 signal、保留证据、支持人工判断”的情报底座。

---

## 已对齐的系统原则

### 1. lane-first
系统的第一公民更接近：

- lane / route / channel
- signal

而不是：

- repo
- user
- run
- 日报 item

原因：同样是 GitHub，也可能存在不同任务意图，例如：

- GitHub 长期跟踪
- GitHub 新项目发现
- GitHub release 监听
- GitHub 文档/README 变化追踪

lane 的价值在于：先定义“为什么收这路信息、这路信息如何处理”，再去接对象和产物。

### 2. signal-first
repo 不是唯一主角，repo 只是 signal 的重要来源和关联对象之一。

更准确的关系是：

- 外部事件/页面/文档变化 → signal
- signal 关联 object（例如 repo）
- 少量 object 升级为长期 watchlist

### 3. 保留原始证据
用户不希望底层只有被加工后的结论，希望能回到：

- release 原文
- changelog 原文 / 关键段落
- README 快照或 diff
- 源链接和时间戳

### 4. 前台弱分区，后台强标签
对外产物不一定要重分区，但系统内部必须保留：

- lane
- source type
- repo/object 关联
- 采集时间
- 原始证据位置

### 5. 半自动
长期观察对象的升级/降级、最终产物选择等，不应全自动执行。
系统先提出建议，人再拍板。

---

## Phase 1 的目标
当前明确选择的是 **最小验证版**。

### 核心命题
要先验证的是：

> 先把信息组织成 lane + signal，这个骨架本身是否成立。

并且第一版：

- **candidate 可以先不要**
- 重点不是自动判断，而是先验证 signal 模型和组织方式是否顺手

### 成功标准
用户确认希望 Phase 1 同时满足：

1. 不同来源的 signal 能稳定地按 lane 收进来
2. 原始证据不丢
3. 能方便回看某一路最近收到了什么
4. 能基于这套结构继续做人工判断

所以 Phase 1 不是“纯采集 demo”，而是一个：

> 可采、可看、可供人工继续决策的最小情报底座。

---

## Phase 1 的 lane 范围
当前收得非常窄：

- 只做 **1 条 lane**
- 名字可暂定为：`github-watch`

这条 lane 的目标不是做全 GitHub 发现，而是只服务于：

> 对既有长期关注 repo 的稳定跟踪

watch 对象来源共识：

- 先从现有核心 watchlist 导入
- 同时允许手工增删

---

## Phase 1 的 signal 范围
当前用户先选了：

- release
- changelog
- README 变化

但这三者在系统里的角色仍需继续收敛。

### README
当前共识：

- README 是“当前状态说明”，不是变化账本
- 但 README 的变化本身可以作为 signal
- 第一版先不自动判断“是否关键变化”
- 只要 README 内容和上次快照不同，就记一条 signal
- 先保存 diff / 快照证据，重要性留给人工判断

### changelog
当前最新对齐：

- changelog 不等于 README
- changelog 也不完全等于 release notes
- changelog 更像“项目维护者记录变化的连续账本”

一句话版本：

> README 讲“我是谁”，release 讲“我发了”，changelog 讲“我到底改了什么”。

对 changelog 的系统理解：

- 它比 README 更适合作为变化证据
- 它比 release 更连续，但缺少统一 API 和标准结构
- 它适合做变化证据层，或在 release 缺位时作为变化来源

在这轮对齐里，用户先选择：

> changelog 在第一版里更像 **和 release 并列的一种独立 signal**。

这意味着 Phase 1 暂不把 changelog 仅仅视为 release 附件，而是认真把它当作 repo 变化来源之一。

---

## 文件与存储形态
用户明确要求：**必须文件优先**。

原因：

- 人可直接读
- AI 可直接检索和消费
- 更容易审计、迁移、重组

### 时间组织单位
- 主单位：**按天组织**

### 目录粒度
选择的是：

- 一天一个索引文件
- 多个独立 signal 文件
- 原始证据单独保存

可参考结构：

```text
signals/
  github-watch/
    2026-03-28/
      index.md
      signals/
        2026-03-28T08-30-12-anthropics-claude-code-release-v2.1.86.md
        2026-03-28T08-45-03-openclaw-openclaw-readme-change.md
        2026-03-28T09-10-20-sst-opencode-changelog-update.md
      evidence/
        anthropics-claude-code-release-v2.1.86.md
        openclaw-openclaw-readme.before.md
        openclaw-openclaw-readme.after.md
        sst-opencode-changelog.before.md
        sst-opencode-changelog.after.md
```

### signal 文件格式
选择的是：

- **Markdown**
- 开头是元数据块
- 后面是正文内容
- 风格类似 skill.md：结构化头部 + human-readable body

### 每日索引文件
选择的是：

- **目录 + 极简摘要**

至少包含：

- 今日有哪些 signal
- 每条 signal 链接
- repo 名
- signal 类型
- 一句很短的变化概述

---

## signal 的唯一性
当前共识：

> 一次 source event 就是一条独立 signal。

也就是：

- 一个 release = 一条 signal
- 一次 README 变化 = 一条 signal
- 一次 changelog 新增/更新 = 一条 signal

第一版先不做“同 repo 同天合并”。
底层先保真、先简单，浏览层以后再考虑轻度聚合。

---

## 从旧 GitHub Daily 系统得到的启发
这轮讨论还回看了之前的 GitHub AI Daily 实现，得到几个关键结论：

### 1. 旧系统本质上是 release-first
之前的 GitHub Daily 实现，对核心仓库主要依赖：

- `gh release view`
- release body
- 从 release body 中抽 bullet
- 再做 repo-specific 中文总结

这说明旧系统本质上不是“通用 repo signal 仓”，而是：

> 以日报产物为目标的 release-first 方案。

### 2. 不是所有 repo 都是 release-driven
已经明确踩过的坑包括：

- 有些 repo 的主要变化在 README / docs
- 有些 repo 更像 manifest/marketplace 驱动
- 有些 repo 不稳定发 release
- 有些 repo（如当时评估的 openclaw/skills）太重，难以稳定追踪

### 3. release 有了，不等于“更新点可直接用”
旧系统后面仍然要写 repo-specific summarizer，说明：

- release body 往往太长、太杂、太英文
- 原始 source 到“中文高信号结论”之间还有较重的加工层

### 4. README / changelog 是旧系统没有真正优雅处理的部分
这也是当前想单独做 signal repo / phase1 的根本原因之一。

---

## 最开始参考的那篇文档：Git scraping
这轮最早、也最有启发的外部材料，是 Simon Willison 的文章：

- **Git scraping: track changes over time by scraping to a Git repository**
- 链接：<https://simonwillison.net/2020/Oct/9/git-scraping/>

这篇文章的核心思路不是“做通知系统”，而是：

1. 有些信息的价值不只在当前状态，而在于“它怎么变了”
2. Git 本身就是很适合保存文本变化历史的工具
3. 用定时任务（例如 GitHub Actions）周期性抓取某个资源
4. 对抓到的结果做适度规范化（如 pretty-print JSON）以提高 diff 可读性
5. 只有内容变化时才提交到 Git
6. 最终得到的是一个可回放、可比较、可审计的变化历史仓库

这篇文档对当前 signal repo 设计最大的启发有三点：

### 启发 1：变化历史本身就是资产
系统不应该只保留“今天的结论”，而应该保留“它相对昨天变了什么”。

### 启发 2：文件 + Git 历史是成立的底座
不一定一上来就需要数据库或复杂事件系统；只要组织得好，文件仓 + Git 就足以承载第一版的变化追踪。

### 启发 3：README / changelog 这类文本对象非常适合做 snapshot-diff
相比 release 这种天然事件，README 和 changelog 更像“状态文本”；它们特别适合用“抓快照 → 对比变化 → 留存证据”的方式建模。

这也是为什么当前 Phase 1 会收敛到：

- 文件优先
- 高保真保留证据
- README 变化可直接作为 signal
- changelog 倾向被认真看作独立变化来源

换句话说，这篇文档给当前系统提供的不是具体 schema，而是一种更底层的方法论：

> 对于文本型信号，最先要做的不是抽象成漂亮结论，而是先把“可比较的历史”保存下来。

---

## 外部参考：其他人通常怎么做
这轮还补看了一些外部做法，发现大致分成三派：

### 1. release 通知派
代表：

- NewReleases.io
- Release Alert

特点：

- 盯 release/tag/package version
- 目标是通知“发版了”
- 稳定，但对 README / 定位变化无能为力

### 2. git-scraping / snapshot-diff 派
代表：

- Simon Willison 的 git scraping 方法

特点：

- 定时抓取某个文本资源
- 规范化后存入 git
- 只要内容变化就形成新历史
- 天然适合 README / docs / 页面类信号

### 3. dependency / ecosystem-aware 派
代表：

- Dependabot / dependabot-core / dependabot CLI

特点：

- 围绕依赖、版本、manifest、release notes 运作
- changelog / release notes 更像升级说明的证据层

这轮外部参考给出的最大启发是：

> release-first 和 snapshot-diff 都是成熟路线；但“把 release、README、changelog 一起纳入一个 lane-first、文件优先的 signal 仓”这件事，反而较少看到现成模板。

这说明当前方向有独特价值，但第一版必须刻意保持最小。

---

## 当前仍待继续问清的问题
虽然已经收得很窄，但仍有几个设计点需要继续拍板：

1. changelog 在 Phase 1 里虽然先选成独立 signal，但其判定口径还要继续细化
   - 是“文件变了就算”
   - 还是“新增了新版本段/新条目才算”
2. release / changelog / README 三类 signal 的优先级与相互去重关系
3. signal 元数据块的最小 schema
4. `github-watch` 每日索引文件的精确模板
5. 后续是否需要专门的 object 视图（当前用户更重视 lane 时间线）

---

## 当前阶段的简化判断
截至 2026-03-28 这轮讨论，可以把当前阶段定义为：

> 先做一个文件优先、按天组织、lane-first 的 GitHub watch signal 仓；只跟踪已有 watchlist repo，先把 release / changelog / README 变化稳定记录下来，并保留足够高保真的证据，供后续人工浏览和判断。

这不是最终系统，而是验证这套骨架是否顺手的第一步。

---

## 附录：daily-lane 中最开始参考的原文（原样收录）
来源：`/Users/haha/workspace/daily-lane/docs/drafts/stage-one-architecture-draft.md`

```md
# Stage One Architecture Draft

Status: draft v1
Scope: stage one only
Repository: `daily-lane`

---

## 1. 背景与问题定义

当前的信息收集链路已经具备不少能力，但整体更像一组一次性脚本的拼接，而不是一个稳定的系统。主要问题不是“抓不到信息”，而是：

- 入口分散
- 流程耦合
- 原始信号、处理中间产物、最终日报混在一起
- 产物质量不稳定
- 很难逐步扩展成真正的产品

因此，第一阶段的目标不是直接重建完整日报系统，而是先搭出一个可长期演化的底层骨架。

---

## 2. 第一阶段目标与非目标

### 2.1 目标

第一阶段要实现的是：

> 一个强声明式、文件系统优先的多路信号召回系统。

它的职责是：

1. 通过多条 lane 从不同来源召回原始信号
2. 以高保真 typed JSONL 的形式输出信号
3. 将这些信号沉淀到统一的 signal pool 中
4. 让 signal pool 同时满足：
   - 人类可检查
   - 程序可消费

### 2.2 非目标

第一阶段明确不做：

- briefs 层
- 日报候选项层
- 卡片生成层
- 最终日报文档层
- 跨路去重
- 路内去重
- 多 signal 合并成一个 brief item 的逻辑
- 复杂排序/评分系统

也就是说，第一阶段先不讨论“怎么写日报”，只讨论“怎么把信号稳定地召回并组织起来”。

---

## 3. 核心架构原则

### 3.1 路（lane）是一等公民

系统的第一公民不是用户、对象、run 或产物，而是 lane。

一条 lane 代表一类真实的信息工作流，例如：

- GitHub 固定 repo 更新
- GitHub 新项目发现
- X 指定账号 / 关键词
- Product Hunt

### 3.2 lane 首先是召回器，不是渲染器

lane 的职责应收敛为：

- 定义召回意图
- 调用对应召回器
- 输出 typed 原始信号
- 控制 quota 和时间窗口

lane 不应过早承担：

- 成稿逻辑
- 卡片逻辑
- 输出格式逻辑
- 重编辑逻辑

### 3.3 高保真优先，不做过早裁剪

lane 层应尽量保留原始信息，而不是提前把信息压缩成摘要。用户偏好是保留原始上下文，而不是只看被洗平的聚合内容。

### 3.4 文件系统优先

第一阶段优先使用目录与文件承载信号池，而不是一开始就把系统做成数据库中心。原因是：

- 易检查
- 易调试
- 易回放
- 易 diff
- 更符合当前阶段“先把系统做清楚”的目标

### 3.5 先做可检查和可消费，不追求聪明

第一阶段的标准不是“自动化得很聪明”，而是：

- 结构清楚
- 输出稳定
- 便于人类检查
- 便于后续程序接入

---

## 4. 分层设计

第一阶段只有两层半：

### 4.1 Lane 层

负责声明与执行多路召回。

### 4.2 Signal 层

负责承载每一路输出的 typed 原始信号。

### 4.3 Signal Pool 层

负责把不同 lane 的 signals 按日期组织起来，并提供基础索引。

> 当前阶段不引入 briefs 层。

未来可以扩展为：

- lane
- signals
- signal pool
- briefs
- documents

但第一阶段先停在 signal pool。

---

## 5. Lane Runtime 设计

### 5.1 配置风格

lane 采用强声明式配置。

这意味着：

- 尽量把“加一路”变成配置动作，而不是开发动作
- 通过 `kind` 决定调用哪种召回器
- 通过参数决定 lane 的具体行为

### 5.2 配置组织方式

第一阶段使用**一个总配置文件**统一列出全部 lanes，而不是一路一个文件。

原因：

- 易总览
- 易统一调整 quota / 开关 / 顺序
- 第一阶段不必过早文件碎片化

### 5.3 lane 最小职责

一条 lane 至少包含：

- 路名 / id
- kind
- quota
- 时间窗口
- 召回参数
- 输出的 signal type

### 5.4 第一阶段 lane kinds

第一阶段优先覆盖 4 个 kinds：

1. GitHub 固定 repo 更新
2. GitHub 新项目发现
3. X 指定账号 / 关键词
4. Product Hunt

这不是最终全集，而是第一阶段足够代表当前主要信息工作流的一组起点。

---

## 6. Signal 协议设计

### 6.1 输出形式

每条 lane 输出 **typed JSONL**。

也就是：

- 一行一个 signal
- 每个 signal 都是结构化 JSON
- 不输出 markdown 摘要作为 lane 直接产物

### 6.2 公共字段最小集

当前公共字段最小集为：

- `type`
- `lane`
- `source`
- `url`
- `fetched_at`
- `title`

### 6.3 schema 策略

采用**半强 schema**：

- 每个 type 有一组必填字段
- 也有一组推荐字段
- 第一阶段允许不同 lane 逐步补齐
- 但不能退化成随意字段集合

### 6.4 type 扩展字段

不同 type 可以带不同字段。

例如：

- `github_release`
- `github_project`
- `x_news`
- `producthunt_product`

每个 type 都可以在公共字段外带自己的专属字段。

---

## 7. Signal Pool 设计

### 7.1 pool 的定位

signal pool 是**轻索引池**，不是纯归档池，也不是计算池。

它负责：

- 收拢所有 lane 的 JSONL
- 提供基础索引
- 让当天召回结果可浏览、可追溯、可程序读取

### 7.2 存储方式

文件系统优先。

### 7.3 目录组织

按日期优先组织：

```text
signals/
  2026-03-22/
    github-release.jsonl
    github-discovery.jsonl
    x-news.jsonl
    producthunt.jsonl
    index.json
```

这种方式最符合当前主消费场景：

> 今天发生了什么、今天各路召回了什么。

### 7.4 index.json

每天目录中必须有一个统一的 `index.json`。

第一阶段它只承载**运行元信息**，不负责复杂统计，不预埋候选层逻辑。

建议至少包含：

- 日期
- 跑了哪些 lanes
- 每条 lane 的状态（成功/失败）
- 每条 lane 的输出文件路径
- 每条 lane 的 signal 数量
- 每条 lane 的配置摘要
- quota / 时间窗口等运行参数

### 7.5 当前不做的事

第一阶段 signal pool 明确不做：

- 跨路去重
- 路内去重
- 候选项生成
- signal 合并
- 对象图谱
- 复杂统计衍生

---

## 8. 样板 Lane：GitHub Release Lane

第一条样板 lane 选为：

> GitHub 固定 repo 的 release lane

之所以选它，是因为：

- 输入边界清晰
- 数据源稳定
- schema 容易先定
- 很适合作为架构样板

### 8.1 当前约束

- 只关心 `release`
- 不处理 PR / issue / commit / README diff / discussion

### 8.2 quota 定义

quota 按 repo 限定，而不是整条 lane 总数限定。

也就是：

- 对每个 repo
- 最多取最近 1 个 release

### 8.3 时间窗口

时间窗口采用：

> 最近 N 天，可配置

### 8.4 `github_release` signal 应尽量保留的字段

第一版尽量全保留，包括但不限于：

- repo 名
- owner/repo
- release 标题
- tag / version
- 发布时间
- release 链接
- release notes 原文
- release body 结构
- assets / 附件
- compare link / source commit（如可拿到）

目标不是“压缩成摘要”，而是完整保留 release 事件本身。

---

## 9. 第一阶段运行流

第一阶段运行流应为：

```text
config
  -> lane runtime
  -> typed JSONL signals
  -> signal pool(date-first)
  -> index.json
```

也就是说：

1. 读取总配置文件
2. 逐条执行 lane
3. 每条 lane 输出自己的 JSONL
4. 写入当天 signal 目录
5. 生成当天 index.json

这一阶段到此结束。

---

## 10. 仓库边界与目录建议

### 10.1 独立仓库

该系统应放在独立仓库中，而不是并入现有 `memory` / `github-daily` / `x-daily` 仓库。

原因：

- 避免历史脚本包袱
- 让新系统有清晰边界
- 后续扩展更自然
- 便于把“代码 / 配置”和“运行结果 / 信号产物”分开管理

### 10.2 运行结果不与代码放在一起

新增明确约束：

> 运行结果不要和代码放在同一个仓库/目录树里。

也就是说，像 `signals/2026-03-22/*.jsonl`、运行生成的 `index.json`、以及未来各种每日产物，不应直接作为源码仓的一部分长期混放。

原因：

- 代码与数据生命周期不同
- 运行结果会持续膨胀，污染源码仓历史
- 会让仓库同时承担“程序源码”和“每日数据湖”的职责，边界变差
- 不利于后续清理、归档、迁移、回放和权限管理

更合理的方向是：

- **代码仓**：只放 runtime、schema、配置、文档
- **运行结果仓 / 数据仓**：放每日 signals、index、以及未来的派生产物

第一阶段可以先在架构层明确这个约束，具体落地形式（独立 data repo、对象存储、外挂目录）后续再定。

### 10.2 建议目录结构

```text
.
├── README.md
├── config/
│   └── lanes.yaml
├── lanes/
│   └── runtimes/
├── signals/
│   └── 2026-03-22/
│       ├── github-release.jsonl
│       ├── x-news.jsonl
│       └── index.json
├── docs/
│   ├── brainstorms/
│   └── drafts/
└── scripts/
```

说明：
- `config/` 放总配置
- `lanes/` 放 runtime 代码
- `signals/` 放 signal pool
- `docs/` 放设计文档与草案

---

## 11. 第一阶段完成标准

第一阶段完成不只是“能跑”，而是同时满足：

### 11.1 人类可检查

- 目录清晰
- JSONL 可读
- index 清晰
- 当天结果可回放
- 某条 lane 的输出可快速核查

### 11.2 程序可消费

- schema 稳定
- 文件命名稳定
- index 可解析
- lane 输出协议稳定
- 后续层可以直接在其上开发，不需要重新猜结构

---

## 12. 第二阶段接口预留

虽然第一阶段先不引入 briefs 层，但应明确预留未来演化方向。

未来可以在 signal pool 之上继续长出：

- briefs 层
- daily 层
- cards 层
- weekly / monthly 层

但这些都属于第二阶段以后的问题。

当前阶段只需要保证：

> signal pool 足够稳定、清晰、可扩展，让未来任何消费层都可以在其上生长。

---

## 13. 总结

第一阶段的正确目标不是“先产出漂亮日报”，而是：

> 先做出一个强声明式、文件系统优先、可检查也可程序消费的多路信号召回系统。

当 lane、typed JSONL、signal pool、index 这四件事立住之后，后面的 briefs、daily、cards 才有可能长成真正的产品，而不是再次退化成一次性脚本拼接。
```
