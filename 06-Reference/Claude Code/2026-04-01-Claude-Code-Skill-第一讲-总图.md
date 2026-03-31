---
title: Claude Code Skill 第一讲：总图
date: 2026-04-01
tags:
  - claude-code
  - skill
  - source-reading
  - learning
status: in-progress
source_repo: https://github.com/T0UGH/cc
---

# Claude Code Skill 第一讲：总图

记录时间：2026-04-01
对应源码仓：`~/workspace/cc`

## 这节课先要建立的心智模型

Claude Code 里的 **skill**，不是“模型自己会的能力”，也不是 plugin。

更准确地说：

> **skill = 可被系统加载和调用的高阶提示词 / 工作流模块**

它的作用不是直接执行操作，而是给模型提供一套：

- 何时使用
- 应该怎么做
- 推荐遵循什么流程
- 有哪些注意事项

所以：

- **tool** 负责“做”
- **skill** 负责“指导怎么做”
- **SkillTool** 是模型访问 skill 的桥，而不是 skill 的执行器

可以记成一句话：

> **tool 是手，skill 是套路。**

---

## 这轮代码扫描看到的 skill 相关入口

### 1. `src/tools/SkillTool/`

这说明 skill 在模型侧不是隐形魔法，而是一个正式工具入口。

它意味着：

- 模型不是直接读取磁盘中所有 skill 文件
- 而是通过 `SkillTool` 去请求“把某个 skill 给我”
- skill 的提供过程是受 runtime 控制的

### 2. `src/skills/loadSkillsDir.ts`

这是当前判断下最核心的 skill 加载器文件。

从搜索结果看，它负责：

- 发现 skill 目录
- 解析 frontmatter
- 创建 skill command
- 做去重
- 处理 unconditional / conditional skill
- 做动态发现与动态激活

如果只选一个 skill 系统核心文件，这个优先级最高。

### 3. `src/skills/bundled/`

这说明 Claude Code 自带一批内置 skill，不完全依赖用户自己写。

已看到的内置 skill 包括：

- `remember`
- `verify`
- `simplify`
- `stuck`
- `skillify`
- `updateConfig`
- 等等

### 4. `src/commands/skills/`

这说明 skill 不只是底层实现概念，也有用户界面层入口。skill 是产品层概念，不是纯内部机制。

---

## skill 不是什么

### skill 不是 plugin

plugin 更像扩展系统能力边界，可能带依赖、能力注入、安装生命周期。

skill 更像：

- 一段可复用的方法论
- 一个工作流模板
- 一组结构化提示内容

所以它更接近 prompt asset / workflow module，而不是 binary extension。

### skill 不是普通 slash command

slash command 更偏用户主动触发的命令入口。

skill 更偏：

- 可被模型发现
- 可按条件激活
- 可作为任务上下文中的“推荐工作方法”注入

因此它比单纯 slash command 更“知识化”。

---

## 从当前代码结果看，skill 系统已经具备的能力

### 1. 多层来源

skill 不只来自一个目录，而是至少包括：

- bundled skills
- user skills
- project skills
- managed skills
- additional dirs
- legacy commands-as-skills

这说明 skill 被设计成一个多层覆盖体系，而不是单点配置。

### 2. 目录化 skill 结构

从 `loadSkillsDir.ts` 搜索结果可见：

> `/skills/` 目录只支持 `skill-name/SKILL.md` 这种结构

这说明 skill 是目录级单元，而不是散落的单个 markdown 文件。

这意味着一个 skill 后续可以自然附带：

- 资源文件
- 相对路径引用
- 配套上下文

### 3. frontmatter 驱动

从搜索到的函数与注释可见，skill 会解析 frontmatter，至少涉及：

- `name`
- `description`
- `whenToUse`
- `paths`
- display name
- hooks / 其他共享字段

也就是说 skill 不只是读正文内容，而是：

> **frontmatter 控行为，正文控内容。**

### 4. 条件激活

skill 至少分成两类：

- unconditional skill
- conditional skill（带 `paths` frontmatter）

条件 skill 会在路径匹配时被激活。这说明 skill 系统具备上下文感知能力。

### 5. 动态发现

代码里明确存在：

- dynamic skill discovery
- 动态加载 skill 目录
- 动态激活 conditional skill

这意味着 skill 不是只在启动时一次性加载，而是可以在 session 运行过程中，随着文件路径与工作区域变化被发现和注入。

---

## 第一讲最重要的五个结论

### 1. skill 是方法，不是能力

能力在 tool，skill 负责告诉模型“这类任务应该怎么做”。

### 2. SkillTool 是访问桥，不是执行器

它负责把 skill 内容提供给模型，而不是直接替模型执行 workflow。

### 3. skill 来自多层目录

bundled / user / project / managed / dynamic discovery 共存。

### 4. skill 有 metadata，不只是 markdown 正文

frontmatter 决定 skill 的行为、激活条件和展示方式。

### 5. skill 支持条件激活和动态发现

这使它比普通 prompt 文件高级很多，也更像“运行时工作流知识模块”。

---

## 当前最推荐的下一步

第二讲应直接进入：

> `src/skills/loadSkillsDir.ts`

因为这个文件最能回答：

- skill 从哪来
- skill 怎么被解析
- skill 怎么被激活
- skill 怎么进入 Claude Code runtime
