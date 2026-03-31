---
title: Claude Code Skill frontmatter 字段速查（humanized）
date: 2026-04-01
tags:
  - claude-code
  - skill
  - frontmatter
  - reference
status: done
source_repo: https://github.com/T0UGH/cc
---

# Claude Code Skill frontmatter 字段速查（humanized）

这份是给自己以后查字段用的，尽量写得直接一点。

先记最重要的一句：

> **最小 skill format 的确只要 `name` 和 `description`。**
> 但从 `src/skills/loadSkillsDir.ts` 来看，Claude Code 其实还能吃下不少可选字段。

所以别把两件事混在一起：

- 对外最常见的写法：很简
- runtime 真正支持的字段：比你平时看到的多

---

## 最小可用

### `name`
skill 的名字。

```yaml
name: 写日报
```

一般就是你最先会写的那个字段。

### `description`
一句话说明这个 skill 是干嘛的。

```yaml
description: 用来整理当天 AI 动态并输出日报草稿
```

虽然系统有 fallback 逻辑，但别省，直接写最稳。

---

## 我觉得最值得先学的字段

### `when_to_use`
告诉模型：什么时候该用这个 skill。

```yaml
when_to_use: 当用户让我整理当天 AI 新闻、GitHub 更新或生成日报时使用
```

这个字段很值。写得清楚，skill 更容易在对的时候被叫出来。

### `arguments`
定义 skill 接收哪些参数。

```yaml
arguments:
  - date
  - topic
```

适合那种“同一个 skill，但每次输入不一样”的场景。

### `argument-hint`
给参数一个提示写法。

```yaml
argument-hint: "<date> <topic>"
```

这个不复杂，就是提示调用格式。

### `allowed-tools`
限制这个 skill 能动哪些工具。

```yaml
allowed-tools:
  - Read
  - Grep
  - Write
```

这个很实用。你不想让某个 skill 乱跑工具，就把范围收住。

### `user-invocable`
控制这个 skill 能不能被用户直接调。

```yaml
user-invocable: true
```

或者：

```yaml
user-invocable: false
```

如果是内部 skill，或者你不想它直接暴露给用户，就可以关掉。

---

## 进阶字段

### `model`
给 skill 指定模型。

```yaml
model: sonnet
```

也可以写：

```yaml
model: inherit
```

`inherit` 的意思就是别覆盖当前模型。

### `effort`
指定 effort。

```yaml
effort: high
```

适合那种你明确希望它多想一层的 skill。

### `version`
给 skill 标个版本。

```yaml
version: 1.0.0
```

不是必须，但多人协作或者后面要演进时挺有用。

### `paths`
这是 conditional skill 的关键字段。

```yaml
paths:
  - "src/**"
  - "docs/**"
```

意思不是“拿这个路径去读文件”，而是：

> 只有当你碰到这些路径下的文件时，这个 skill 才会被激活。

这个字段很适合项目级 skill。

---

## 高级字段：先知道，不着急乱用

### `disable-model-invocation`

```yaml
disable-model-invocation: true
```

会影响 skill 的运行方式。先知道有这个开关就行。

### `context`

```yaml
context: fork
```

源码里目前明确识别的是 `fork`。

### `agent`

```yaml
agent: researcher
```

更像多 agent / 特定 agent 场景里的字段。

### `hooks`

```yaml
hooks:
  pre:
    - echo "before"
  post:
    - echo "after"
```

这个会过 schema 校验，说明不是随便写什么都行。

### `shell`

```yaml
shell:
  enabled: true
```

和 skill 里的 shell 行为有关。这个先别乱碰，等把 skill runtime 再看深一点再说。

---

## 给自己一个简单分层

### 现在就建议用

```yaml
name
description
when_to_use
arguments
argument-hint
allowed-tools
user-invocable
```

### 熟一点再加

```yaml
paths
model
effort
version
```

### 先知道有就行

```yaml
disable-model-invocation
context
agent
hooks
shell
```

---

## 一个稳妥版模板

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

## 最后留一句最好记的话

> **最小 skill format 只有 `name + description`。**
> **真正常用的 skill format，大概率还会再加 `when_to_use + arguments + allowed-tools`。**
> 其他字段，大多属于进阶玩法。
