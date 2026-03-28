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
- evidence：**内嵌优先**
- 文件命名：**repo + type + 时间/版本锚点**
- `index.md`：**纯索引**

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

## 九、后续真正值得继续问的问题

接下来更值得继续推进的，不再是“收不收 release/changelog/README”，而是：

1. markdown signal files 的最小 metadata 协议如何定义
2. signal pool 的目录组织是 `date-first` 还是 `lane/date hybrid`
3. evidence 文件与 signal 文件如何关联最稳
4. `github-watch` 作为样板 lane，最小实现边界如何收紧
5. 将来从 signal pool 长出 candidate / brief 层时，如何避免反向污染 signal 层
