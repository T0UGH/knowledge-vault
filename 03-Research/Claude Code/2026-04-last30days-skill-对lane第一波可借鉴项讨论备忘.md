---
title: last30days-skill 对 lane 第一波可借鉴项讨论备忘
date: 2026-04-06
tags:
  - last30days-skill
  - lane
  - discussion-note
  - Obsidian
status: draft
related_notes:
  - 03-Research/Claude Code/2026-04-last30days-skill-项目探索调研报告.md
  - 03-Research/Claude Code/2026-04-last30days-skill-快速阅读-哪些设计值得抄到lane上.md
---

# last30days-skill 对 lane 第一波可借鉴项讨论备忘

## 这页是干嘛的

这不是正式实施计划，也不是要立刻改 lane。

这页只记录这次讨论收敛出来的一个前提：

> **不要太动 lane 的本质。**

也就是：

- 不改 lane 作为稳定脚本 / 收集层的底层定位
- 不把 topic research 流程硬塞进 lane 主链
- 不在前层引入更多 AI 决策
- 第一波只看“低风险、增强稳定性、不会改味”的借鉴项

这页是给明天继续讨论用的备忘版本。

---

## 当前共识：第一波只借“运行纪律层”，不借“research 产品层”

如果只看第一波，当前更适合借的不是 `last30days-skill` 的 topic research 主流程，而是它的工程配套。

当前讨论里收敛出的第一波候选只有四类：

1. `--diagnose`
2. `--check-config`
3. `--mock` + fixtures
4. source status / quality status 输出

共同点：

- 不改 lane 主流程
- 不改 signal 层的底层原则
- 不动 lane 的对象模型
- 只增强可观测性、可排障性、可回放性

一句话版：

> **第一波不是让 lane 更聪明，而是让 lane 更像一套可靠生产系统。**

---

## 为什么第一波只看这几项

原因很简单：

`last30days-skill` 有两层东西：

### 第一层：research 产品层
例如：
- topic-driven 研究入口
- 多平台讨论聚合
- comparative mode
- prompting / recommendations / general query flow
- NUX / first-run setup / interactive wizard

### 第二层：运行纪律层
例如：
- 配置检查
- source availability 诊断
- mock / fixtures
- 状态输出
- 更清晰的运行边界

现在对 lane 来说，真正低风险、值得先拿的，是第二层。

因为第一层一旦借多了，很容易把 lane 从：

- 稳定收集层

带偏成：

- topic research 系统

这不是现在想要的方向。

---

## 第一波可借鉴项 1：`--diagnose`

这是当前优先级最高的一项。

它的目标不是执行主流程，而是回答：

> **这条 lane 现在有没有条件正常运行？**

比如最小诊断可以包括：

- 配置文件在不在
- token / key 在不在
- 输出目录可不可写
- 状态文件 / 状态库是否存在
- 关键依赖是否可用
- 各 source 当前是否可抓

### 对 lane 的意义

它不改变收集逻辑，只是把“问题是配置、依赖、权限，还是业务逻辑”区分开。

这非常适合先落在最核心的 1~2 条 lane 上，而不是一上来全铺开。

---

## 第一波可借鉴项 2：`--check-config`

这个比 `--diagnose` 更窄，专门只看配置。

主要检查：

- 必填配置缺失
- 配置值格式不合法
- 冲突配置
- 废弃配置项
- 默认值是否正确回退

### 与 `--diagnose` 的区别

- `--check-config`：只检查配置本身对不对
- `--diagnose`：检查整个运行环境健不健康

如果第一波只想做得更简单，也可以先把 `--check-config` 并进 `--diagnose` 里，后面再拆。

---

## 第一波可借鉴项 3：`--mock` + fixtures

这组东西的目标不是替代真实运行，而是提供：

> **一个可回放、可重复、可本地验证的最小运行环境。**

### fixtures 是什么

可以把它理解成：

- 提前保存好的样本输入
- 标准化的“假数据”
- 用于验证主流程的固定材料

例如在 `github-watch` 语境下，可以是：

- 某次 release API 返回结果
- 某次 changelog 内容
- 某次 README 变化前后版本
- 空结果样本
- API 错误样本

### `--mock` 是什么

就是让 lane 在不连真实外部源的情况下，直接吃这些样本跑一遍完整流程。

关键点不是“伪造整个系统”，而是：

> **只替换输入，不改后面的 normalize / render / status 等主流程。**

这样才能真正回答：

- 我这次改脚本有没有把输出搞坏
- 空结果分支还能不能跑
- 某类 signal 的结构有没有被改崩

### 为什么这适合第一波

因为它不会改 lane 的对象模型，只是补了一层回放能力。

---

## 第一波可借鉴项 4：status 输出

这是一次运行结束后，附带给人的简洁状态说明。

它的作用不是“总结内容”，而是“解释这次运行的状态”。

例如可以包括：

- 监控对象数
- release / changelog / README 各抓到多少条
- 空结果对象数
- 失败对象数
- 是否降级运行
- 是否缺某类输入源

### 它解决的问题

避免把下面两种情况混在一起：

- 今天真的没什么变化
- 今天链路没跑好

对日报与周期性收集链路，这个差别非常关键。

---

## fixtures / `--mock` / `--diagnose` / status 的直白解释

为了防止后面讨论又抽象化，这里保留一版最直白说明。

### fixtures
就是提前存好的“样本数据 / 样题库”。

### `--mock`
就是拿这些样本数据跑一次模拟执行。

### `--diagnose`
就是开机自检 / 健康检查。

### `--check-config`
就是先检查参数表有没有填错。

### status 输出
就是每次运行完的成绩单 + 异常说明。

---

## 用 `github-watch` 举的最小例子

为了让讨论更贴 lane，之前已经举过一个最小例子，这里只保留核心要点。

### fixtures 可以长这样

```text
github-watch/
├─ fixtures/
│  ├─ release_claude_code.json
│  ├─ changelog_opencode.md
│  ├─ readme_codex_before.md
│  ├─ readme_codex_after.md
│  ├─ empty_result.json
│  └─ api_error.json
```

### `--mock` 的意义

```bash
python3 scripts/github_watch.py --mock
```

不真连 GitHub，而是直接吃 fixtures 跑：

- normalize
- render
- status

这样就能快速判断：

- 输出结构有没有坏
- 某类 signal 分支有没有炸
- 空结果 / 错误结果还能不能过完整流程

### `--diagnose` 的意义

```bash
python3 scripts/github_watch.py --diagnose
```

输出类似：

- token 是否存在
- watchlist 是否加载成功
- 输出目录是否可写
- 状态库是否正常
- release / changelog / README source 是否可用

### status 的意义

跑完以后明确写出：

- 这次监控了多少 repo
- 真抓到了多少种 signal
- 有多少 repo 失败
- 是否降级运行

---

## 当前明确“不借”的东西

为了避免明天继续讨论时范围再次膨胀，这里也记下当前明确不建议在第一波借的内容。

### 不借 1：topic-driven research 主流程
不把 `/last30days <topic>` 这套逻辑塞进 lane 主链。

### 不借 2：复杂 scoring / relevance / AI 判断
第一波不追求让 lane 更“聪明”，只追求更稳。

### 不借 3：watchlist / briefing / history 的大改造
这些可能以后有价值，但第一波先不动。

### 不借 4：first-run wizard / NUX
这更像面向通用 skill 用户的产品设计，不是 lane 当前最需要的。

---

## 如果只允许先做一件事

当前讨论里，最值得先落地的一项仍然是：

> **先给最核心的一条 lane 补 `--diagnose`。**

原因：

- 风险最低
- 不改主流程
- 不改数据结构
- 对排障帮助最大
- 最符合“先增强稳定性，不改本质”的要求

---

## 明天继续讨论时，最值得接着问的问题

为了减少重新找上下文，明天可以直接接这几个问题：

1. 第一波要落在哪一条 lane 上？
   - `github-watch`？
   - 还是别的更核心链路？

2. `--diagnose` 最小检查项有哪些？
   - 哪些是必须
   - 哪些可以后补

3. fixtures 要不要第一波就上？
   - 如果上，先做哪 3~5 个最关键样本？

4. status 输出要写到哪里？
   - 终端输出
   - 日志文件
   - 每日报告附注
   - 还是状态 JSON

5. `--check-config` 是单独做，还是先并入 `--diagnose`？

---

## 当前一句话结论

> **对 lane 的第一波借鉴，应该只拿 `last30days-skill` 的运行纪律层：`--diagnose`、`--check-config`、`--mock + fixtures`、status 输出。**
>
> **这些东西能让 lane 更稳，但不会把 lane 变成另一个系统。**
