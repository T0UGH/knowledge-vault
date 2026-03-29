---
title: Daily Lane Index Redesign PRD for Arc
date: 2026-03-29
tags:
  - daily-lane
  - prd
  - index-redesign
  - arc
status: draft
source_repo: https://github.com/T0UGH/daily-lane
---

# Daily Lane Index Redesign PRD for Arc

这份 PRD 的目标很单纯：

> **请 Arc 先改 `daily-lane` 几条现有 lane 的 `index.md`，让它们从“归档索引”变成“agent 可导航入口”。**

这次**不要求 Arc 端到端跑通日报内容层**，也不要求同时把 cron / editor / reviewer 整套都做完。

当前只聚焦：

- 现有 lane 的 `index.md` 重设计
- 保持 lane 层仍然是稳定脚本
- 不引入 AI
- 优先服务后续的日报内容层 v1

---

## 一、背景

当前 `daily-lane` 的底层召回层已经基本成形，已有五条 lane：

- `github-watch`
- `product-hunt-watch`
- `github-trending-weekly`
- `x-feed`
- `x-following`

日报内容层 v1 已经拍定为：

- 两个 subagent + 一个 cron
- cron 第一版只喂各 lane `index.md`
- Editor / Reviewer 按需自己回查原始 signal
- 第一版先只产出完整 markdown 日报
- 按 lane 展开，不要顶部重点

这意味着：

> **`index.md` 不能再只是文件列表，而必须成为 agent 工作时的导航入口。**

---

## 二、这次任务的范围（Scope）

### 要做的

1. 重设计几条现有 lane 的 `index.md` 输出
2. 让 `index.md` 更适合 Editor / Reviewer agent 导航和回查
3. 保持实现方式为**稳定脚本**
4. 保持 lane 层**无 AI**

### 这次不做的

1. 不要求 E2E 跑完整日报内容层
2. 不要求实现 cron / editor / reviewer 整套工作流
3. 不要求定义 reviewer rubric
4. 不要求定义完整 markdown 日报模板
5. 不要求引入 candidate 层 / rules engine / DSL

---

## 三、核心原则

### 1. lane 层仍然只是稳定脚本

这次 index redesign 仍然属于 lane 层工作。

不要把 lane 变成：

- AI 预筛器
- candidate 生成器
- 日报编辑器
- 规则系统

### 2. 不允许 AI

`index.md` 的补强必须完全通过：

- 现有 signal frontmatter
- 现有 signal 正文
- 稳定脚本摘取 / 清洗 / 截断 / 聚合

来完成。

不允许：

- AI 生成导航提示
- AI 生成更可读标题
- AI 做内容判断
- AI 做预筛选

### 3. `index.md` 的角色是“agent 可导航入口”

它不是：

- 纯归档目录
- candidate 层
- 日报层

它只负责让后续 agent 更快知道：

- 这一 lane 今天有什么
- 哪些 signal 值得点开
- 应该先点开哪个 signal 文件

---

## 四、统一设计方向

### 采用：公共底座 + lane 自定义附加字段

不要做成：

- 全部 lane 一套死模板
- 各 lane 完全毫无共性

应该做成：

- 有统一底座，保证后续 agent 使用稳定
- 同时保留 lane 自己的语义特征

---

## 五、公共底座字段（已拍定）

每条 signal 在 `index.md` 中，至少应提供以下**可见字段**：

- `type`
- `title`
- `fetched_at`
- `signal_link`
- `source_url`
- `1句导航提示`

### 字段说明

#### `type`
- 必须出现在 index 的可见文本里
- 这是给 agent 的导航线索，不只是内部元信息

#### `title`
- **分 lane 决定**
- 有些 lane 可以直接沿用原 signal 标题
- 有些 lane 可以用稳定脚本重组为更可读标题
- 但**不允许 AI**

#### `fetched_at`
- 统一作为公共底座时间字段
- 不要求当前阶段强行统一“内容发生时间”语义
- 各 lane 若需要额外展示 `published_at` / `created_at` / `featured_at`，应作为 lane 自定义附加字段

#### `signal_link`
- 指向 data repo 内的 signal 文件
- 供 Editor / Reviewer 直接点开看原始 signal 正文

#### `source_url`
- 指向原始来源页面
- 供 Editor / Reviewer 回到原站核对

#### `1句导航提示`
- 必须存在
- 作用是帮助 agent 快速判断“要不要进一步点开”
- 不是摘要层，不是 AI 层，不是编辑判断

---

## 六、对象字段命名规则（已拍定）

不要强行统一成抽象 `object` 字段。

按 lane 自然表达：

- GitHub → `repo`
- Product Hunt → `product`
- X → `author`

也就是说：

- 统一的是“index 里要告诉 agent 这条 signal 主要挂在哪个主对象上”
- 不统一抽象命名本身

---

## 七、导航提示规则（强约束）

### 强 A：只能脚本摘取

“1句导航提示”必须：

> **只能从现有 signal 的结构化字段或正文里，用稳定脚本摘取。**

### 允许的方式

- 取 frontmatter 已有字段
- 取固定 section 第一行
- 取 bullet 列表第一条
- 取 description / tagline / preview
- 做稳定截断、去换行、清洗

### 不允许的方式

- AI 重写
- AI 摘要
- AI 生成更像人话的导航提示
- AI 做内容判断

---

## 八、lane 优先级建议

这次不要求 Arc 一口气把所有 lane 打磨到最优。

建议优先顺序：

### 第一优先级
1. `github-watch`
2. `product-hunt-watch`

原因：
- 当前这两条的 index 明显不够用
- 最影响“cron 只喂 index”这套机制能否成立

### 第二优先级
3. `github-trending-weekly`
4. `x-feed`
5. `x-following`

原因：
- 后三条已经相对更接近可用
- 但仍值得按统一原则补强

---

## 九、每条 lane 的实现方向（只给方向，不先写死细节字段表）

### `github-watch`
当前问题：
- 现在更像文件列表
- 缺少足够导航信息

方向：
- 保留 `repo`
- 补强 release / changelog / readme 变化的可导航提示
- 让 agent 从 index 就能知道“这条 release / change 大概是什么”

### `product-hunt-watch`
当前问题：
- topic hit 平铺过重
- 同产品重复出现多次
- 缺少一句“这产品是干什么的”

方向：
- 更偏 `product` 视角暴露信息
- 减少纯 topic 命中平铺造成的导航负担
- 让 agent 从 index 就能知道产品基本定位

### `github-trending-weekly`
方向：
- 利用现有正文中已有的 rank / language / stars / description / README 信息
- 提供更强导航入口

### `x-feed`
方向：
- 保留当前 preview / engagement 优势
- 继续增强导航性，但不要过度加工

### `x-following`
方向：
- 保留当前 author / group / preview / engagement
- 提高长列表场景下的导航效率

---

## 十、验收标准（本次只验 index redesign）

Arc 这轮提交不按 E2E 日报闭环验收，而按下面标准验收：

### 必须满足
1. 现有目标 lane 的 `index.md` 已按统一原则重做
2. lane 层仍然是稳定脚本
3. 没有引入 AI
4. 公共底座字段已出现
5. 导航提示可稳定由脚本产生
6. index 明显比当前版本更适合 agent 导航

### 不要求本次完成
1. 不要求完整日报产出
2. 不要求 cron / editor / reviewer 跑通
3. 不要求 prompt 完善
4. 不要求 reviewer rubric 完整化

---

## 十一、交付形式

Arc 这轮建议交付：

1. 一版 implementation design（简短即可）
2. 对应 lane 脚本修改
3. 更新后的 sample `index.md` 产物
4. try-run 证据
5. 明确说明哪些字段是公共底座，哪些是 lane 自定义

---

## 十二、一句话版任务说明

> 请先不要做日报内容层 E2E，也不要上规则系统。先只做 `daily-lane` 现有几条 lane 的 `index.md` redesign：把它们从归档索引改成 agent 可导航入口，采用“公共底座 + lane 自定义字段”的结构，保持 lane 层完全脚本化、无 AI，并优先补最弱的 `github-watch` 与 `product-hunt-watch`。
