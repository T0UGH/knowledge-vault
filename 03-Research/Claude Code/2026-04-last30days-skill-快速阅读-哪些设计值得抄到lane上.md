---
title: last30days-skill 快速阅读：哪些设计值得抄到 lane 上
date: 2026-04-05
tags:
  - last30days-skill
  - lane
  - skill
  - research
  - Obsidian
status: done
source_files:
  - /Users/haha/.openclaw/workspace/last30days-skill/README.md
  - /Users/haha/.openclaw/workspace/last30days-skill/SKILL.md
  - /Users/haha/.openclaw/workspace/last30days-skill/SPEC.md
  - /Users/haha/.openclaw/workspace/last30days-skill/TASKS.md
  - /Users/haha/.openclaw/workspace/last30days-skill/scripts/last30days.py
---

# last30days-skill 快速阅读：哪些设计值得抄到 lane 上

## 这篇看什么

这不是细读源码，而是一次面向工程借鉴的快速阅读。

目标很明确：

> **看 `mvanhorn/last30days-skill` 里，有哪些设计不是它这个 research skill 独有，而是可以直接抄到 lane 上的。**

我这次主要扫了：

- `README.md`
- `SKILL.md`
- `SPEC.md`
- `TASKS.md`
- `scripts/last30days.py`
- `scripts/lib/*`
- `tests/*`

## 先给主结论

如果只先记一句话，我会留这个版本：

> `last30days-skill` 最值得抄的，不是“研究过去 30 天”这个产品点子，而是它把一个 skill 做成了一个**可配置、可诊断、可测试、可演进的能力包**。对 lane 来说，真正值得拿走的是这套工程骨架，而不是它表面的多源 research 功能。**

再压缩一点，就是：

- **能力要有 spec**
- **执行要有主入口和模块分层**
- **运行要有 diagnose / mock / fixtures / tests**
- **数据流要显式拆成 normalize / score / dedupe / render / store**

一句最短版：

> **可以抄的是“怎么把 lane 做成产品级稳定脚本”，不是“照搬一个 research 聚合器”。**

---

## 一、最值得抄的 8 件事

## 1. 把 lane 从“脚本集合”抬成“能力包”

这个仓库不是只有 `SKILL.md`，而是整套配齐：

- `SKILL.md`
- `.claude-plugin/plugin.json`
- `SPEC.md`
- `TASKS.md`
- `README.md`
- `CHANGELOG.md`
- `tests/`

说明作者不是把它当成“一个能跑的脚本”，而是：

> **一个有边界、有输入输出、有维护路径的能力包。**

### lane 可以直接抄
每条重要 lane 最少补：
- `SPEC.md`
- `TASKS.md`
- `README.md`
- `tests/`
- `CHANGELOG.md`

---

## 2. 主入口脚本 + `lib/` 模块分层

它的结构非常清楚：

- `scripts/last30days.py` = orchestrator
- `scripts/lib/*` = 各能力模块

比如：
- `env.py`
- `query.py`
- `relevance.py`
- `score.py`
- `dedupe.py`
- `render.py`
- `normalize.py`

这说明它不是把所有逻辑堆进一个脚本，而是：

> **入口负责编排，核心能力下沉到 lib。**

### lane 可以直接抄
统一成：
- `run_xxx_lane.py` / `lane_xxx.py`
- `lib/config.py`
- `lib/collect.py`
- `lib/normalize.py`
- `lib/score.py`
- `lib/dedupe.py`
- `lib/render.py`
- `lib/store.py`

---

## 3. `--diagnose` / setup / config check

这个 skill 很强调：

- `--diagnose`
- setup wizard
- config check
- source availability

这点对 lane 非常值。

### lane 可以直接抄
给每条重要 lane 都做：
- `--diagnose`
- `--dry-run`
- `--mock`
- `--check-config`

至少能快速回答：
- 配置齐不齐
- 数据源通不通
- 依赖在不在
- 这条 lane 今天能不能跑

---

## 4. `fixtures/` + `--mock` + `tests/`

它不是只有 tests，而是完整配了：

- `fixtures/`
- `--mock`
- 大量 `tests/test_*.py`

这非常适合 lane。

### lane 可以直接抄
每条重要 lane 都配：
- `fixtures/input_*.json`
- `fixtures/output_*.md`
- `--mock`
- `tests/test_normalize.py`
- `tests/test_score.py`
- `tests/test_render.py`
- `tests/test_dates.py`

这和之前定过的 lane 原则完全一致：

> lane 层尽量是稳定脚本，不把 AI 决策塞进召回层。

既然是稳定脚本，就该能回放、能回归测试。

---

## 5. 配置分层

它 README 里明确区分：

- 全局配置：`~/.config/last30days/.env`
- 项目级覆盖：`.claude/last30days.env`

### lane 可以直接抄
做成：
- 全局：`~/.config/lane/*.env`
- 项目级：`.lane/*.env` 或 `.claude/*.env`

这样可以做到：
- 全局默认规则统一
- 项目局部覆盖不污染全局

---

## 6. one-shot、watchlist、briefing、store 分层

这个仓库里有几个很关键的脚本：

- `watchlist.py`
- `briefing.py`
- `store.py`

README 里也明确区分：

- one-shot research
- watchlist
- briefing
- history

这背后的方法论非常适合 lane：

> **不要把“一次运行”和“长期积累”混成一坨。**

### lane 可以直接抄
拆成三层：
- collect
- store
- briefing / digest / render

这和你已经定下的底层原则几乎完全同构。

---

## 7. 显式 `score / dedupe / relevance`

这个 skill 在数据处理层非常明确：

- `score.py`
- `dedupe.py`
- `relevance.py`
- `cross_source` tests

说明它不是采完就直接吐给用户，而是：

> **有一条明确的数据处理管线。**

### lane 可以直接抄
采集后统一经过：
- normalize
- score
- dedupe
- render

而不是把这些逻辑全塞在“生成日报”的一个脚本里。

---

## 8. 安全 / 权限 / 数据边界写清楚

它在 `SKILL.md` 和 README 里明确写了：

- 会访问哪些源
- 会写到哪里
- 哪些 key 必要、哪些可选
- 哪些数据会出本机

### lane 可以直接抄
每条重要 lane 文档至少写清：
- 输入源
- 输出位置
- 是否联网
- 是否会写外部 repo
- 是否会发通知
- 失败会留什么日志

这会直接提升：
- 交接效率
- 安全边界清晰度
- 维护信心

---

## 二、我不建议直接照搬的东西

## 1. 不要照搬它超长的 `SKILL.md`

它这个 skill 很产品化，面向终端用户，所以有很重的：
- first-run wizard
- setup 话术
- 渐进式 source unlock 文案

lane 未必需要这么重。

对 lane 真正值得抄的是：
- 结构
- 配置分层
- diagnose
- fixtures / tests
- 模块化

而不是冗长 onboarding 文案。

## 2. 不要把 lane 做成“源越多越好”

这个 skill 多源是合理的，因为它做的是 research 聚合器。

但 lane 已经有一条很明确的原则：

> 召回/收集层优先稳定脚本，不在最前面引入太多 AI 决策。

所以 lane 更该学的是它的**工程纪律**，不是功能膨胀。

---

## 三、如果只提炼成 5 条可执行建议

### 1. 每条重要 lane 都做成：
- 一个主入口脚本
- 一个 `lib/` 目录
- 一个 `tests/` 目录
- 一个 `fixtures/` 目录

### 2. 每条 lane 都要有：
- `--diagnose`
- `--mock`
- `--dry-run`

### 3. 每条 lane 的文档至少包含：
- 输入源
- 输出位置
- 配置项
- 安全边界
- 失败排查入口

### 4. 采集后统一拆成：
- normalize
- score
- dedupe
- render
- store

### 5. one-shot 与长期积累分开
不要把：
- 抓一次
- 存历史
- 做 briefing
- 生成报告

全塞进同一个脚本里。

---

## 四、一句话定义

如果让我给这篇总结留一个最短定义，我会写：

> `last30days-skill` 对 lane 最有价值的启发，不是“研究过去 30 天”这个具体功能，而是它示范了：怎么把一条能力链做成有 spec、有诊断、有测试、有分层、有长期积累接口的稳定能力包。**

## 下一步最顺怎么接

如果后面要继续利用这次阅读成果，最顺的不是继续深读这个仓库，而是：

1. 先把“可抄的 lane 骨架”抽出来
2. 再选你现有一条 lane 试着按这个骨架重构

如果只选一个，我会建议先做第 1 个。