---
title: Claude Code Skill frontmatter 字段速查
date: 2026-04-01
tags:
  - claude-code
  - skill
  - frontmatter
  - reference
status: done
source_repo: https://github.com/T0UGH/cc
---

# Claude Code Skill frontmatter 字段速查

记录时间：2026-04-01
对应源码仓：`~/workspace/cc`
主要依据：`src/skills/loadSkillsDir.ts`

## 背景

在学习 Claude Code skill 系统时，发现一个容易混淆的点：

- 对外常见的最小 skill format 往往只强调 `name` 和 `description`
- 但从 runtime loader 源码看，Claude Code 实际支持一组更完整的可选 frontmatter 字段

因此需要一份“字段速查”，区分：

- 最小必需
- 常用增强
- 高级运行时字段

---

## 总结版结论

> **最小 skill format 确实可以只写 `name` 和 `description`，**
> 但 Claude Code runtime 还能解析多种可选 frontmatter 字段，用于控制 skill 的展示、参数、工具约束、条件激活和运行时行为。

---

## 一、基础字段

### `name`

**作用：** skill 的名字 / 显示名。

**示例：**

```yaml
name: 写日报
```

**说明：** 最基础字段之一。

---

### `description`

**作用：** skill 的简短描述。

**示例：**

```yaml
description: 用于整理当天 AI 动态并输出日报初稿
```

**说明：** 推荐始终显式写。若未写，系统可能从正文里抽 fallback 描述，但不要依赖这个行为。

---

## 二、推荐常用字段

### `when_to_use`

**作用：** 告诉模型这个 skill 适用于什么场景。

**示例：**

```yaml
when_to_use: 当用户让我整理当天 AI 新闻、GitHub 更新或生成日报时使用
```

**说明：** 对 skill 的正确触发非常重要。

---

### `arguments`

**作用：** 定义 skill 接收哪些参数。

**示例：**

```yaml
arguments:
  - date
  - topic
```

**说明：** 适合需要带参调用的 skill。

---

### `argument-hint`

**作用：** 给 skill 参数提供提示说明。

**示例：**

```yaml
argument-hint: "<date> <topic>"
```

**说明：** 更像参数使用提示文案。

---

### `allowed-tools`

**作用：** 声明 / 限制该 skill 允许使用的工具。

**示例：**

```yaml
allowed-tools:
  - Read
  - Grep
  - Write
```

**说明：** 非常实用。适合让 skill 的执行范围更聚焦。

---

### `user-invocable`

**作用：** 控制 skill 是否能被用户直接调用。

**示例：**

```yaml
user-invocable: true
```

或：

```yaml
user-invocable: false
```

**说明：** 默认通常是 `true`。若为 `false`，更像系统内部/模型内部使用的 skill。

---

## 三、高级运行时字段

### `model`

**作用：** 为该 skill 指定模型。

**示例：**

```yaml
model: sonnet
```

或：

```yaml
model: inherit
```

**说明：** 若写 `inherit`，表示不覆盖当前模型。

---

### `effort`

**作用：** 指定 skill 的 effort / 思考强度。

**示例：**

```yaml
effort: high
```

**说明：** 适合需要更深入分析的 skill。

---

### `disable-model-invocation`

**作用：** 控制该 skill 是否禁用模型调用。

**示例：**

```yaml
disable-model-invocation: true
```

**说明：** 高级字段，未完全搞清运行链路前不建议乱用。

---

### `context`

**作用：** 指定 skill 的执行上下文。

**示例：**

```yaml
context: fork
```

**说明：** 当前源码中明确识别 `fork`。

---

### `agent`

**作用：** 给该 skill 指定 agent。

**示例：**

```yaml
agent: researcher
```

**说明：** 偏高级，多 agent 场景才更有意义。

---

### `paths`

**作用：** 定义 skill 的路径匹配规则，使其成为 conditional skill。

**示例：**

```yaml
paths:
  - "src/**"
  - "docs/**"
```

**说明：** 当操作文件命中这些路径时，skill 才会被激活。

---

### `hooks`

**作用：** 给 skill 配置 hooks。

**示例：**

```yaml
hooks:
  pre:
    - echo "before"
  post:
    - echo "after"
```

**说明：** 会经过 `HooksSchema()` 校验。高级能力，格式不对会被忽略。

---

### `shell`

**作用：** 配置 shell 相关行为。

**示例：**

```yaml
shell:
  enabled: true
```

**说明：** 与 skill 中 prompt shell 执行能力有关。高级字段。

---

### `version`

**作用：** 给 skill 标版本。

**示例：**

```yaml
version: 1.0.0
```

**说明：** 方便维护 skill 演进。

---

## 四、推荐使用分层

### 现在就建议用

- `name`
- `description`
- `when_to_use`
- `arguments`
- `argument-hint`
- `allowed-tools`
- `user-invocable`

### 等更熟再用

- `paths`
- `model`
- `effort`
- `version`

### 先知道有，但别急着乱用

- `hooks`
- `shell`
- `context`
- `agent`
- `disable-model-invocation`

---

## 五、推荐的稳妥版 template

```md
---
name: 生成日报
description: 读取当天素材并整理成结构清晰的日报草稿
when_to_use: 当用户要求整理当天动态、生成日报、汇总更新时使用
arguments:
  - date
  - topic
argument-hint: "<date> <topic>"
allowed-tools:
  - Read
  - Grep
  - Write
user-invocable: true
---

请按下面流程工作：

1. 先读取输入资料
2. 提取核心更新点
3. 按主题整理
4. 输出中文日报草稿

如果传入了 `${date}`，优先围绕该日期整理。
如果传入了 `${topic}`，优先突出该主题。
```

---

## 最短记忆版

### 最小集

```yaml
name
description
```

### 推荐集

```yaml
name
description
when_to_use
arguments
argument-hint
allowed-tools
user-invocable
```

### 高级集

```yaml
model
effort
paths
version
hooks
shell
context
agent
disable-model-invocation
```
