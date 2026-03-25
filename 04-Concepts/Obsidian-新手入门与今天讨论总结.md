---
title: Obsidian 新手入门与今天讨论总结
date: 2026-03-25
tags:
  - obsidian
  - knowledge-management
  - note-taking
  - agent-workflow
aliases:
  - Obsidian 新手扫盲
  - Obsidian 基础概念
---

# Obsidian 新手入门与今天讨论总结

这篇笔记记录今天围绕 [[Obsidian]]、[[knowledge-vault]]、agent skills 和 [[OpenClaw]] 的讨论，目标是给未来的自己留下一份 **适合新手回看** 的入门说明。

> [!summary]
> 今天的核心结论不是“又装了一个工具”，而是：
> - `knowledge-vault` 已经成为统一的 Obsidian 主仓
> - Obsidian 的本体是 **本地 Markdown 文件 + GUI 工作台**
> - 给 agent 用的 Obsidian skills 也已经接入
> - [[OpenClaw]] 现在可以作为总控入口来操作这个仓库

## 1. Obsidian 到底是什么

一句话理解：

**Obsidian 不是云笔记服务，本质上是一个读取和编辑本地 Markdown 文件的 GUI App。**

也就是说：

- 你的笔记本质上是一堆 `.md` 文件
- Obsidian 只是一个强大的阅读、编辑、组织工具
- 数据属于你自己，不锁在平台里

这个思路更像：

- [[VS Code]] 对代码文件
- Obsidian 对笔记文件

## 2. Vault 是什么

[[Vault]] 是 Obsidian 最核心的概念。

**Vault 本质上就是一个文件夹。**

比如：

- `/Users/wangguiping/workspace/knowledge-vault`

这个目录就是当前的主 vault。

不是数据库，不是账号空间，不是云端容器。

## 3. Note 是什么

一篇 note，本质上就是一个 Markdown 文件。

例如：

- `Obsidian-新手入门与今天讨论总结.md`
- `OpenClaw 使用笔记.md`
- `2026-03-25.md`

也就是说，笔记并不是“存进 Obsidian 的黑盒数据库”，而是真实存在于文件系统里的文本文件。

## 4. Markdown 是什么

[[Markdown]] 是一种轻量文本格式。

优点：

- 纯文本
- 稳定
- 易迁移
- AI 容易读写
- 适合长期保存

这也是为什么 Obsidian 对工程师很友好。

## 5. 文件夹、链接、标签分别负责什么

这个地方最容易混淆。

### 文件夹（Folders）
负责 **管理边界**。

例如：

- `01-Inbox/`
- `02-Projects/`
- `03-Research/`
- `04-Concepts/`

### 内部链接（Wikilinks）
负责 **知识关联**。

写法是：

```md
[[某篇笔记]]
```

它表示链接到 vault 内另一篇笔记。

### 标签（Tags）
负责 **轻量分类和检索辅助**。

例如：

- `#obsidian`
- `#knowledge-management`

> [!tip]
> 新手阶段不要过度依赖标签。更稳的做法是：
> - 文件夹管理边界
> - 链接管理关系
> - 标签只做辅助

## 6. 什么是双链和反向链接

这是 Obsidian 最出名的概念，但本质并不复杂。

如果笔记 A 写了：

```md
[[笔记 B]]
```

那么：

- A 链接到了 B
- B 也会知道“有一篇笔记提到了我”

后者就是 [[Backlinks]]（反向链接）。

它的价值在于：

**你不仅知道自己链接了谁，还知道谁曾经引用过当前主题。**

## 7. Graph View 该怎么看

[[Graph View]] 是把笔记和链接关系画成图。

它很酷，但不是核心生产力。

更准确的判断是：

- 适合看整体结构
- 适合探索知识簇
- 不适合代替目录和日常检索

> [!warning]
> 新手不要把主要精力先放在 Graph View 上。
> 真正有价值的是：
> - 写得下来
> - 找得到
> - 能复用
> - 能持续整理

## 8. Plugin、Template、Theme 是什么

### Plugin
插件，给 Obsidian 增加功能。

### Template
模板，给笔记提供固定骨架。

### Theme
主题，改变界面样式。

对于新手：

- 先不要装一堆插件
- 先学会 vault / note / folder / link
- 能稳定写几篇笔记之后，再慢慢加插件

## 9. Obsidian 有 GUI App 吗

有，而且官方主形态就是桌面 GUI App。

官方主页：

- [Obsidian 官网](https://obsidian.md/)

GitHub 相关仓库：

- [obsidianmd/obsidian-releases](https://github.com/obsidianmd/obsidian-releases)

## 10. Obsidian 有给 agent 用的 skill 吗

有，而且 GitHub 上已经有比较成形的项目。

其中一个代表仓库：

- [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills)

它的目标不是给人点按钮，而是：

**教 agent 如何正确理解和操作 Obsidian 生态。**

比如：

- `obsidian-markdown`
- `obsidian-bases`
- `json-canvas`
- `obsidian-cli`
- `defuddle`

这些都属于“给 agent 的行为协议层”。

## 11. 这些 skill 和 Obsidian 插件不是一回事

这里必须区分三层：

### 第一层：Obsidian App / Vault
数据和 GUI 本体。

### 第二层：Plugin / CLI
能力执行层。

### 第三层：Skill
给 agent 的操作规范和行为约束。

所以 skill 不是替代 Obsidian，而是：

**让 agent 学会如何正确使用 Obsidian。**

## 12. 今天已经完成了什么安装

### 在 `knowledge-vault` 中安装
已安装到：

- `.claude/skills/`

包括：

- `obsidian-markdown`
- `obsidian-bases`
- `json-canvas`
- `obsidian-cli`
- `defuddle`

这更偏向给 [[Claude Code]] 使用。

### 在 [[OpenClaw]] 中全局安装
已安装到：

- `~/.openclaw/skills/`

并已经验证 `Ready`：

- `obsidian-markdown`
- `obsidian-bases`
- `json-canvas`
- `obsidian-cli`
- `defuddle`

这意味着现在 [[OpenClaw]] 主会话也能直接使用这些 skills。

## 13. OpenClaw 在这个体系里扮演什么角色

最容易理解的模型是：

- [[knowledge-vault]] = 数据层
- Obsidian skills = 行为协议层
- [[Claude Code]] = 仓库内执行器
- [[OpenClaw]] = 总控台 / 编排层

也就是说：

**OpenClaw 不是 Obsidian 本身，而是帮助协调 agent 去操作 vault 的入口。**

## 14. 为什么建立 `knowledge-vault` 主仓很重要

这一步的意义不在于“建了个 GitHub repo”，而在于你把知识系统的边界正式收口了。

核心价值：

- 有统一主仓
- 有统一 remote
- 有明确主线 `main`
- 有未来自动化的锚点

这意味着知识管理开始从“个人习惯”升级成“工程系统”。

## 15. 给新手的最小可行理解

如果只记住最重要的 6 句话，可以记这几个：

1. Obsidian 本质上是一个 Markdown 笔记工具
2. Vault 本质上就是一个文件夹
3. 一篇 Note 本质上就是一个 `.md` 文件
4. `[[笔记名]]` 是内部链接
5. Backlinks 是“谁提到了我”
6. 目录负责管理，链接负责关联

## 16. 现在的推荐使用方式

现阶段最合理的工作流：

1. 先把 `knowledge-vault` 当作统一主仓使用
2. 先理解基本概念：vault / note / folder / link
3. 需要结构化整理时，让 [[OpenClaw]] 直接帮忙改仓库
4. 后续如果 [[Claude Code]] 恢复稳定，再利用 `.claude/skills` 做更深度的持续整理

> [!success]
> 当前阶段最重要的不是折腾更多插件，而是：
> - 持续写
> - 稳定归档
> - 形成目录和命名习惯
> - 让 agent 写入行为可控

## 17. 后续可以继续学习什么

下一步适合继续补的主题：

- [[Obsidian 目录结构设计]]
- [[Obsidian Wikilink 使用原则]]
- [[Obsidian Template 模板设计]]
- [[OpenClaw 与 Obsidian 协作流]]
- [[Agent 写入 Obsidian 的规范]]

## 18. 一句话总结

今天的收获可以压缩成一句话：

**我已经有了一个统一的 Obsidian 主仓，并且开始把它从“记笔记的地方”升级成“人和 agent 共用的知识工作台”。**
