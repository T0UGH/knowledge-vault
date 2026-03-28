---
title: Daily Lane Stage One Architecture v2
date: 2026-03-28
tags:
  - daily-lane
  - signal-system
  - architecture
  - phase1
status: draft
source_repo: https://github.com/T0UGH/daily-lane
source_doc: docs/drafts/stage-one-architecture-draft.md
---

# Daily Lane / Stage One Architecture v2

这份笔记是对 `daily-lane` 中 `docs/drafts/stage-one-architecture-draft.md` 的 2026-03-28 版修订整理，目的是把最近一轮关于 signal system / phase1 的结论沉淀到 Obsidian 仓库，方便后续持续检索与继续设计。

---

## 一、这套系统的本体

这不是一个日报生成器，也不是一个 repo watch 小工具，而是一个：

> **lane-first、signal-first 的情报系统。**

日报、卡片、brief、候选项、长期观察面，都属于它未来可能长出的消费层或上层能力；但第一阶段先不碰这些。

当前最小目标是先把：

```text
lane -> signal -> signal pool
```

这个底层闭环立住。

---

## 二、Phase 1 的最小闭环

### 当前明确选择

Phase 1 只停在：

- lane
- signal
- signal pool

### 不包含

- candidate / 日报候选项
- briefs
- 最终日报文档层
- object registry / watch registry
- 复杂排序 / 评分 / 聚合
- signal 层去重 / 合并

所以第一阶段不是“做日报”，而是验证：

1. lane 作为召回组织单位是否成立
2. signal 作为原始信息单元是否成立
3. signal pool 作为文件优先、按天归档的中间层是否成立

---

## 三、核心原则

### 1. lane 是第一公民

系统的第一公民不是：

- 用户订阅配置
- 长期对象
- run
- 产物

而是 lane。

原因是：同样是 GitHub，也可能对应不同工作流：

- GitHub 长期跟踪
- GitHub 新项目发现
- release 跟踪
- README / changelog 变化跟踪

lane 的本质是：

> 定义“为什么收这一路信息，以及这一路如何召回”。

### 2. signal-first

repo 不是唯一主角，repo 只是 signal 的重要来源或关联对象之一。

更准确地说：

- 外部变化 / 页面 / 文档 / release / changelog / README diff → signal
- signal 未来可以再被上层消费
- 少量对象以后才可能进入长期观察面管理

### 3. signal 层只负责收集

当前最重要的新共识是：

> **signal 层只负责收集，尽量无损保留信息。**

所以 signal 层：

- 不做候选筛选
- 不做 signal 合并
- 不做 signal 层去重
- 不为了“看起来简洁”而裁剪信息

即使同一天同一 repo 同时出现：

- release
- changelog 更新
- README 变化

底层也都要分别记录。

### 4. lane 层应是稳定脚本，而不是 AI 决策器

贵平后来又补了一条更高优先级的大原则：

> **每个 lane 都应尽量做成一个精心设计、可重复、可审计的稳定脚本。**

这意味着：

- lane 可以用 shell、Python 或其他合适语言实现
- lane 的关键不是“聪明”，而是规则清楚、行为稳定、输入输出可控
- 不应在 lane / 召回层引入 AI 决策
- AI 的主要发挥位置应后移到 signal 之上的信息处理层

### 5. 文件优先

这一轮最关键的修订之一，是从旧 draft 的 **typed JSONL** 正式改成：

> **markdown signal files**

原因：

- 人类可直接阅读
- AI 可直接检索和消费
- 更适合保真、审计、回放
- 更符合当前阶段“先把系统做顺”的目标

---

## 四、对旧 draft 的关键修订

### 旧 draft 的核心表述
旧版 Stage One Draft 更偏：

- lane 直接产出 typed JSONL
- signal pool 收拢 JSONL
- 先从多类 lane 起步
- 把 signal pool 视为轻索引池

### 当前修订版的关键变化

#### 1. typed JSONL → markdown signal files
这是当前最明确、最正式的底层变更。

现在 signal 的主承载形式改为：

- markdown 文件
- metadata 头部
- human-readable body
- 配套 evidence 文件

#### 2. 多 lane 起步 → 单 lane 样板起步
旧 draft 原本倾向第一阶段覆盖：

- GitHub 固定 repo 更新
- GitHub 新项目发现
- X 指定账号 / 关键词
- Product Hunt

当前则明确收成：

- 第一阶段只先落一条样板 lane：`github-watch`

这不是否定多 lane，而是收缩为最小验证版。

#### 3. signal pool 定位进一步收窄
旧 draft 把 signal pool 视为“轻索引池”。

当前更明确地把它定义为：

> **按天归档的信号池**

也就是：

- 重点是归档、追溯、可检查
- 不把它定义成浏览产品入口
- index 主要承载归档和运行元信息，而不是消费层逻辑

#### 4. 强化“signal 层不裁剪”原则
旧 draft 里虽然强调高保真，但这轮把边界进一步说死了：

- 不做 signal 层去重
- 不做 signal 层合并
- 不做候选筛选式裁剪

---

## 五、Phase 1 的样板 lane：`github-watch`

### 为什么只是样板，不是系统本体
`github-watch` 只是第一条样板 lane，因为它：

- 边界相对清晰
- 能代表“长期跟踪型 lane”
- 适合验证 markdown signal files 是否顺手

它不是整个系统等于 GitHub watch，而只是第一条用来试运行骨架的 lane。

### watchlist 的位置
在第一阶段里：

> watchlist 只是 lane 的输入配置。

它不是独立系统层，也不是 object registry。

### `github-watch` 第一阶段收什么
当前已经拍板：三类都收。

- `release`
- `changelog`
- `README` 变化

并且三者都作为独立 signal 记录。

### 三类 signal 的触发方式
这一步不引入 AI 判断，而是尽量用稳定脚本规则完成：

- **release**：检测到新 release 事件，就产生一条 release signal
- **changelog**：当前 changelog 文件与上次保存版本不同，就产生一条 changelog signal
- **README**：当前 README 文件与上次保存版本不同，就产生一条 README signal

也就是 README / changelog 的第一阶段规则都收敛为：

> 文件变了，就记 signal。

### 推荐实现顺序
Arc 的建议我认同，也值得写死成执行原则：

1. 先做 **release**，把最简单、最稳的一条链先跑通
2. 再加 **changelog**，顺势把 `state/` 快照管理问题解决
3. 最后再加 **README**

所以三类 signal 是“架构上同时保留”，但不是“工程上同时开工”。

### 三者的角色差异

#### release
- 正式发布事件
- 边界最清晰
- 最稳

#### changelog
- 变化账本
- 回答“到底改了什么”
- 不只是 release 附件，而是独立变化来源

#### README
- 状态说明文档
- 内容变化本身可作为 signal
- 噪音可能更高，但仍应保留

---

## 六、signal pool 应该长什么样

### 当前定位
signal pool 是：

- lane-first
- 在 lane 之下按天归档
- 文件优先
- 可追溯
- 可读
- 可供程序和 AI 继续消费
- 对需要 diff 的 signal 类型，允许在 lane 下维护 `state/` 目录保存上次快照

### 当前不承担的职责
signal pool 当前不承担：

- candidate 生成
- 浏览产品体验优化
- 聚合视图
- 对象图谱
- 自动摘要
- 独立 run 记录层

### 推荐理解
可以把 signal pool 理解成：

> 一个 lane-first、日期归档型的中间信号档案层。

不是日报，不是 brief，不是最终产品界面。`index.md` 只做纯索引；运行过程排错交给脚本日志。

---

## 七、signal file 的当前共识

截至目前，这一层已经比较清楚：

- 主承载形式：**markdown signal file**
- 目录主轴：**lane-first**
- 正文主轴：**原始证据优先**
- metadata：**尽量薄，只承担最小检索与归属职责**
- frontmatter：**必须保持机器可解析，至少保留最小公共字段**
- evidence：**内嵌优先**
- 文件命名：**repo + type + 时间/版本锚点**
- `index.md`：**纯索引，但不能退化成毫无信息的文件名列表**

也就是说，signal file 更像一份自足的原始记录单元，而不是一张结构化大表或浏览卡片。

## 八、第一阶段的判断标准

第一阶段完成，不只是“能跑”，而是同时满足：

### 1. 人类可检查
- 目录清晰
- signal 文件可读
- evidence 可追溯
- index 清晰
- 某条 lane 的输出可快速核查
- 脚本日志可用于排错与补跑

### 2. AI / 程序可消费
- 最小公共字段稳定
- 文件结构稳定
- index 可解析
- lane 输出协议稳定

### 3. 原始信息不被过早裁剪
也就是：

- source evidence 尽量保留
- README / changelog / release 不提前揉平
- 同日同 repo 多种变化可并存

---

## 八、当前一句话版结论

截至 2026-03-28，这套系统的第一阶段可以概括为：

> 先做一个 lane-first、signal-first、文件优先的底层信号系统；以 `github-watch` 作为第一条样板 lane，把 `release`、`changelog`、`README` 变化都以 markdown signal files 的形式无损保留下来，并按天沉淀到 signal pool 中，暂不进入 candidate、brief 和最终产物层。

---

## 九、给 Arc 的 design brief（执行入口）

如果接下来要由 Arc 基于当前文档继续写 implementation design，可直接按下面这份简化 brief 开始。

### 1. Phase 1 要做什么

- 实现最小闭环：`lane -> signal -> signal pool`
- 第一阶段只落一条样板 lane：`github-watch`
- signal 主承载形式为 markdown signal files
- signal pool 为 lane-first、按天归档的文件系统结构

### 2. Phase 1 明确不做什么

- 不做 candidate / brief / document 输出层
- 不做 object registry / watch registry
- 不做 signal 层去重 / 合并
- 不做 AI 驱动的 lane 决策
- 不做独立 materialize / normalize 层
- 不做浏览产品层

### 3. lane 实现原则

- lane 是精心设计、可重复、可审计的稳定脚本
- 语言不限，shell / Python 均可
- lane 直接产出 signal 文件
- 排错依赖脚本日志，不单独设计 run 记录层

### 4. `github-watch` 的第一阶段 scope

同时保留三类 signal：

- `release`
- `changelog`
- `README`

但工程落地顺序明确为：

1. 先 `release`
2. 再 `changelog`
3. 最后 `README`

### 5. signal file 最低约束

- 每条 signal 一个 markdown 文件
- frontmatter 必须机器可解析
- frontmatter 至少包含：
  - `type`
  - `lane`
  - `source`
  - `url`
  - `fetched_at`
  - `repo`（或等价对象标识）
  - `title`
- 正文原始证据优先
- evidence 内嵌优先

### 6. signal pool / state 约束

推荐目录骨架：

```text
signals/
  github-watch/
    state/
      <repo-state-files>
    YYYY-MM-DD/
      index.md
      signals/
        <signal-files>
      evidence/
        <evidence-files>
```

其中：
- `state/` 用于保存 changelog / README 的上次快照基线
- `index.md` 只做纯索引，但不能退化成毫无信息的裸文件名列表

### 7. 最低验收标准

- 跑通 release signal 采集
- 正确落到 signal pool
- 生成当天 `index.md`
- 具备 changelog / README 所需 `state/`
- 再继续补 changelog signal
- 最后补 README signal

## 十、后续真正值得继续问的问题

接下来更值得继续推进的，不再是“收不收 release/changelog/README”，而是：

1. markdown signal files 的最小 metadata 协议如何定义
2. signal pool 的目录组织如何在实现中落稳
3. evidence 文件与 signal 文件如何关联最稳
4. `github-watch` 作为样板 lane，最小实现边界如何收紧
5. 将来从 signal pool 长出 candidate / brief 层时，如何避免反向污染 signal 层

---

## 十一、四条 lane 的当前盘点

截至 2026-03-28，贵平明确的目标不是只做 `github-watch` 一条 lane，而是让此前已经以脚本方式跑过的几类信息入口，逐步继承到这套新的 lane-first / signal-first 系统里。

当前明确的四条 lane 是：

1. `github-watch`
   - 已实现 Phase 1 样板版本
   - 当前承担长期跟踪型 GitHub 信号收集
   - 已验证 `release / changelog / README` 这类结构化或半结构化来源如何进入 signal pool

2. `github-discovery`
   - 对应 GitHub Trending、新项目发掘与追踪
   - 本质更偏 discovery lane，而不是长期对象 watch lane
   - 后续设计时需要特别警惕它滑向 candidate / 排序 / 推荐层

3. `x-watchlist`
   - 对应 X 上指定 watch list 的监控
   - 更像“固定来源列表”的长期跟踪型 lane
   - 与 `github-watch` 相比，来源形态不同，但“白名单对象 → 稳定收集”的思路是相通的

4. `x-feed`
   - 对应 X Feed 自动刷流
   - 输入开放度更高、噪音更大、边界更容易漂移
   - 最容易不小心越过 signal 层，提前滑向 candidate / 去噪 / AI 筛选

5. `product-hunt-watch`
   - 对应 Product Hunt 网站的追踪
   - 来源单一、结构相对规整
   - 非常适合作为 `github-watch` 之后的第二条 lane 样板

### 说明

这里虽然列出了 5 个名字，但本质上是“`github-watch` 已完成 + 另外 4 类待继承入口”里的产品盘点。若按这轮对话里用户列举的“接下来要继续做的四条 lane”理解，可视为：

- `github-discovery`
- `x-watchlist`
- `x-feed`
- `product-hunt-watch`

也就是在 `github-watch` 之外，还要把另外四类脚本化来源逐步接进同一套 signal system。

---

## 十二、推荐实现顺序

当前最合理的顺序不是按“重要性”排，而是按：

- 复用现有骨架的难度
- 是否容易验证 lane-first 抽象真的成立
- 是否容易不小心越界到 Phase 2

基于这个标准，当前推荐顺序是：

1. `product-hunt-watch`
2. `x-watchlist`
3. `x-feed`
4. `github-discovery`

### 1. 为什么先做 `product-hunt-watch`

这是当前最合适的第二条 lane，因为它：

- 来源单一
- 结构相对简单
- 不像 X 那样容易牵扯复杂登录态、刷流策略、噪音控制
- 也不像 GitHub trending / 新项目发现那样天然贴近“候选项”与“推荐层”

更重要的是，它适合作为：

> **第一条非 GitHub release 型、且可以先不依赖 `state/` 的 lane 样板。**

如果 `product-hunt-watch` 能顺利接入，说明当前这套系统并不是只对 `github-watch` 特化，而是真能承载第二种来源形态。

### 2. 为什么 `x-watchlist` 放在 `product-hunt-watch` 后面

`x-watchlist` 虽然也属于明确来源列表型 lane，但相比 Product Hunt，仍更容易遇到：

- 登录态 / API / 采集通道稳定性问题
- 文本、线程、转推、媒体等内容形态差异
- 同一账号高频更新导致的信号密度问题

所以它适合在第二条 lane 骨架验证成功之后再做。

### 3. 为什么 `x-feed` 不宜太早做

`x-feed` 是当前最容易失控的一条 lane，因为它更接近开放输入流，而不是固定对象监控。它的问题不是“能不能抓”，而是：

- 噪音如何接受
- 信号密度如何控制
- 不做 AI 筛选时如何保持 lane 仍然有价值
- 如何避免它偷偷长成 candidate / ranking / summary 层

因此它不适合作为第二条 lane 样板。

### 4. 为什么 `github-discovery` 放最后

虽然 `github-discovery` 仍然属于 GitHub，但它和 `github-watch` 的本质不一样。它更偏：

- discovery
- trending / ranking
- 新项目判断
- 候选项倾向

也因此最容易越过当前 Phase 1 的边界，滑向“候选发现系统”而不是“signal 收集系统”。

所以它更适合放到：

- `github-watch` 已经证明长期跟踪型 lane 可行
- `product-hunt-watch` 已经证明 stateless / 列表型 lane 可行
- `x-watchlist` / `x-feed` 已经验证社交流输入如何接入

之后再做。

---

## 十三、接下来 brainstorm 的焦点

既然当前推荐下一条 lane 是 `product-hunt-watch`，那后续 brainstorm 最值得明确的，不是泛泛讨论四条 lane，而是把 Product Hunt 这一条收成一个最小、稳定、可继承到现有系统里的实现边界。

也就是重点回答：

1. `product-hunt-watch` 的 signal 类型第一版只收什么
2. Product Hunt 第一版是否需要 `state/`，还是先做 stateless lane
3. Product Hunt 的 signal file frontmatter 最小字段里，需要哪些 source-specific 字段
4. `index.md` 在 Product Hunt lane 下最低应该呈现哪些信息
5. 如何明确它只做 signal 收集，而不滑向推荐、排序和日报候选层
