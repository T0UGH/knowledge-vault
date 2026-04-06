# 2026-04-06｜Signal Engine v1 internal model notes

## 背景

这页用来收敛 Signal Engine 第一版在“内部事实源 / 内部对象 / 外部产物”上的核心设计决策。

这轮讨论的目标不是命令树或迁移顺序，而是回答：

> Signal Engine v1 在内部到底以什么为真相？这些真相如何落地成 markdown / index / run.json，而不重新长成两套系统？

---

## 一句话总结

Signal Engine v1 的内部设计原则应该是：

> **内存对象优先，文件为派生落地；内部模型与外部持久化协议明确分离。**

更具体地说：
- 运行时以内存对象为事实源
- 持久化仍以 markdown / state files / run.json 为主
- 不引入 DB 作为第一版事实源
- `SignalRecord` 与 `RunResult` 是核心内部对象
- `index.md` 明确降级为派生产物
- `run.json` 是运行收据，不是 `RunResult` 的直接全量序列化

---

## 一、Signal Engine v1 的存储原则

### 已拍板

#### 1. 第一版采用：**内存对象 + 文件落地**
不引入数据库作为第一版事实源。

也就是：
- 运行时事实源：Python 内存对象
- 持久化事实源：
  - `signals/*.md`
  - `index.md`
  - `state/*`
  - `run.json`

#### 2. 第一版不上 DB
原因不是数据库不好，而是第一版如果上 DB，会立刻引入：
- schema migration
- 表设计
- DB 与 markdown/state 的双事实源竞争
- diagnose/status 到底读哪一层的问题

当前阶段这些成本高于收益。

---

## 二、Signal Engine v1 的内部对象

### 已拍板

#### 1. 第一版明确使用 **typed model**
不走隐式 dict 漂移路线。

#### 2. typed model 第一版先用 **Python `dataclass`**
而不是一开始就上 Pydantic。

原因：
- 第一版不上 DB
- 第一版也不需要复杂外部 schema 契约
- `dataclass` 足够轻、足够清楚、足够适合测试与 render

### 核心内部对象

#### A. `SignalRecord`
表示单条 signal 的内部事实源。

第一版字段边界已收敛为（B 方案）：
- `signal_type`
- `source`
- `title`
- `source_url`
- `fetched_at`
- `file_path`
- `lane`
- `entity_id`
- `entity_type`

这个边界的意义是：
- 比极简版更 lane-aware / domain-aware
- 但还没胖到复制一整套 frontmatter

#### B. `RunResult`
表示一次 lane/day collect run 的内部事实源。

至少承载：
- lane
- date
- status
- started_at / finished_at
- warnings
- errors
- counts
- `signal_records[]`
- index 文件路径等运行级信息

### 关键拍板

#### 3. `RunResult` 在运行时内存里保留完整 `SignalRecord[]`
但 `run.json` 不全量落这些内容。

这意味着：
- 内部事实源足够完整
- 外部协议保持克制
- 避免把 `run.json` 做成第二套 signal catalog

---

## 三、内部对象与外部产物的关系

### 已拍板

#### 1. `SignalRecord` 先成型，再统一渲染 signal markdown
也就是：
- 对象优先
- 文件派生

而不是“先拼 markdown，再倒推出结构”。

这很关键，因为它决定了未来：
- markdown 不是事实源
- 渲染层可以独立测试
- 不同输出不会各自偷长业务逻辑

#### 2. `index.md` 在第一版中明确降级为派生产物
也就是：
- 它继续保留，继续兼容现有链路
- 但架构上不再被视为事实源

这能避免新系统继续被旧 Markdown 牵着走。

#### 3. `run.json` 不直接等于 `RunResult` 的全量序列化
而是通过单独 mapper/render 生成。

这条是本轮一个非常关键的架构决策。

原因：
- `RunResult` 是内部运行对象
- `run.json` 是外部持久化协议
- 两者必须解耦

如果直接 dump `RunResult`，未来很容易出现：
- 内部字段外泄
- schema 跟着内部实现一起漂
- `run.json` 逐渐长胖

所以第一版应明确采用：

> `RunResult` → `RunManifestMapper` / `render_run_manifest()` → `run.json`

---

## 四、`run.json` 的定位

### 已拍板

#### 1. `run.json` 是 **run-level manifest / 运行收据**
不是第二套 signal 内容系统。

#### 2. 每 lane/day 一份
例如：
- `signals/github-watch/2026-04-06/run.json`
- `signals/x-feed/2026-04-06/run.json`

#### 3. 覆盖写
第一版不保留 attempts history。

#### 4. 成功失败都写
因为它本来就是“运行收据”。

#### 5. 第一版最小字段已拍板
- `lane`
- `date`
- `status`
- `started_at`
- `finished_at`
- `warnings`
- `errors`
- `summary.repos_checked`
- `summary.signals_written`
- `summary.signal_types`
- `artifacts.index_file`
- `artifacts.signal_files`

### 设计含义

`run.json` 的角色是：
- 给 runtime/status/diagnose 读
- 给 agent 先做 run-level 概览
- 给后续 collect system 自己做可观测性

而不是：
- 承载 signal 正文
- 承载 ranking/filtering 结果
- 承载 processing datasets

---

## 五、Signal Engine v1 内部设计的核心原则

这轮收敛下来，第一版最重要的不是功能多少，而是边界不串味。

### 应守住的原则

#### 原则 1
**内部对象是事实源，文件是派生产物。**

#### 原则 2
**外部产物不反向绑架内部模型。**
例如：
- `run.json` 不因方便而偷吃内部全部字段
- `index.md` 不再作为架构核心

#### 原则 3
**collect / runtime-support / future processing 三层不要串味。**
第一版只到 collect，不应为了 `run.json` 或 diagnose 偷偷引入 processing 语义。

#### 原则 4
**Signal Engine v1 先是 runtime，不是平台。**
不做 plugin system、不做 dynamic registry、不做过重平台化。

---

## 六、一句话结论

> Signal Engine v1 应该采用“`dataclass` 内部对象 + 文件落地”的设计：`SignalRecord` 和 `RunResult` 作为内部事实源，markdown/index/run.json 都由它们派生出来；其中 `index.md` 明确降级为兼容产物，`run.json` 通过单独 mapper 渲染成稳定的运行收据，而不是内部对象的直接倾倒。
