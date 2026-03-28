---
title: Anthropic - Effective harnesses for long-running agents 共读笔记
date: 2026-03-28
tags:
  - anthropic
  - agents
  - harness
  - reading-notes
  - ai
aliases:
  - Effective harnesses for long-running agents 共读笔记
---

# Anthropic - Effective harnesses for long-running agents 共读笔记

原文：[[Anthropic - Effective harnesses for long-running agents]]

## 完整中文译文

### 开头三段

随着 AI agent 能力越来越强，开发者开始越来越多地让它们承担复杂任务，这些任务往往会持续数小时，甚至数天。  
但一个尚未被真正解决的问题是：**如何让 agent 在多个 context window 之间持续、稳定地推进工作。**

长时间运行的 agent 的核心挑战在于：它们必须在**离散的 session** 中工作，而每个新 session 开始时，都**不记得之前发生过什么**。  
你可以想象一个软件项目由轮班工程师负责，但每个新来的工程师都完全不知道上一个班次干了什么。  
由于 context window 有限制，而大多数复杂项目又不可能在一个窗口里做完，所以 agent 需要一种机制，来弥合不同 coding session 之间的断层。

我们提出了一个两段式方案，让 Claude Agent SDK 能在多个 context window 之间更有效地工作：

1. 一个 **initializer agent**，在首次运行时负责把环境搭好  
2. 一个 **coding agent**，负责在每个 session 中做增量推进，同时为下一个 session 留下清晰的交接产物

配套的 quickstart 里可以看到代码示例。

## The long-running agent problem

Claude Agent SDK 是一个功能很强的通用型 agent harness，既擅长编码，也适合处理那些需要模型借助工具来**收集上下文、制定计划并执行**的任务。  
它具备像 compaction 这样的上下文管理能力，使 agent 可以在不耗尽 context window 的情况下持续工作。  
从理论上说，在这样的机制下，agent 应该能够在任意长的时间里持续做出有用的工作。

但是，**仅靠 compaction 还不够**。  
即使是像 Opus 4.5 这样的前沿 coding model，运行在 Claude Agent SDK 上，并且可以在多个 context window 中循环工作，如果它拿到的只有一个高层次 prompt，比如“构建一个 claude.ai 的克隆”，它依然不足以真正做出一个生产可用质量的 web app。

Claude 的失败主要表现为两种模式。  
第一种是，agent 倾向于一次做太多事——本质上是在试图“一把梭”把整个应用做完。  
这通常会导致模型在实现过程中途耗尽上下文，使得下一个 session 开始时面对的是一个**功能做到一半、而且没有文档说明**的状态。  
接下来的 agent 就只能去猜前一个 session 到底做了什么，并且花大量时间试图把最基础的应用重新修回可运行状态。  
即使有 compaction，这种情况仍然会发生，因为 compaction 也不总能把清晰明确的交接指令传给下一个 agent。

第二种失败模式通常出现在项目的后期。  
当一些功能已经做出来之后，后续某个 agent 实例会四处看一眼，发现“好像已经有进展了”，然后就直接宣布任务完成。

这就把问题拆成了两个部分。  
第一，我们需要先搭建一个初始环境，为 prompt 所要求的**全部功能**打好基础，让 agent 能按**一步一步、一个功能一个功能**的方式推进。  
第二，我们应该要求每个 agent 只做**增量推进**，并且在 session 结束时，把环境留在一个**干净状态**。  
所谓“干净状态”，指的是那种已经适合合并进主分支的代码状态：没有重大 bug，代码结构有序、文档清楚，而且整体上，一个开发者可以直接开始做下一个功能，而不需要先去清理一堆无关的烂摊子。

在内部实验中，我们用一个两段式方案来解决这些问题：

1. **Initializer agent**：第一个 agent session 使用一个专门设计的 prompt，要求模型先把初始环境搭好，包括：  
   - 一个 `init.sh` 脚本  
   - 一个 `claude-progress.txt` 文件，用来记录各个 agent 做过什么  
   - 一个初始 git commit，用来明确最开始新增了哪些文件  
2. **Coding agent**：后续每个 session 都要求模型只做增量推进，并留下结构化的更新记录。

这里最关键的洞察是：**必须找到一种办法，让 agent 在拿到一个全新的 context window 时，能够迅速理解当前工作的状态。**  
他们是通过 `claude-progress.txt` 加上 git 历史来实现这一点的。  
而这套做法的灵感，其实正来自优秀软件工程师的日常工作方式。

## Environment management

在更新后的 Claude 4 prompting guide 中，他们分享了一些多 context window 工作流的最佳实践，其中包括一种 harness 结构：**在第一个 context window 使用不同的 prompt。**  
这个“不同的 prompt”会要求 initializer agent 先把环境搭好，并把未来 coding agent 高效工作所需的上下文准备齐全。  
接下来，这篇文章会更深入地拆解这种环境中的几个关键组成部分。

### Feature list

为了应对 agent 想“一把梭”做完整个 app、或者过早判断项目已完成的问题，他们要求 initializer agent 先写出一份**完整的功能需求文件**，把用户最初的 prompt 进一步展开。  
在那个 claude.ai 克隆案例里，这意味着要拆出 **200 多个功能点**，例如：

- 用户可以打开一个新聊天
- 输入问题
- 按下回车
- 看到 AI 回复

这些功能点在一开始都被标记为 **failing**，这样后续的 coding agent 就能清楚知道：**什么才算完整功能，什么还没做完。**

示例结构大意如下：

- `category`：功能类别  
- `description`：功能描述  
- `steps`：端到端验证步骤  
- `passes`：当前是否通过

他们要求 coding agent 对这份文件的修改**只能通过修改 `passes` 字段的状态来完成**，并且用了非常强硬的措辞，例如：

> “删除或修改测试是不可接受的，因为这会导致功能缺失或出现 bug。”

经过一段时间实验，他们最终选择用 **JSON** 来保存这份文件，而不是 Markdown。  
原因是：相比 Markdown，模型更不容易对 JSON 文件做出不恰当的改写、覆盖或随意重写。

### Incremental progress

在有了前面的初始环境脚手架之后，后续的 coding agent 就被要求**一次只处理一个功能点**。  
这种增量式推进的方法，事实证明对于解决 agent 总想一次做太多事情的问题至关重要。

即使已经改成增量推进，仍然有一个关键要求：模型每次完成代码修改后，都必须把环境留在一个**干净状态**。  
在他们的实验中，他们发现，要诱导模型形成这种行为，最有效的方法是要求模型：

- 把进展提交到 git，并写清楚描述性的 commit message  
- 同时在 progress file 中写下本次进展的总结

这样一来，模型就可以利用 git 回退那些糟糕的代码修改，并恢复代码库之前的可工作状态。

这些做法还提升了整体效率，因为它们消除了这样一种浪费：

- agent 不再需要花时间去猜前面到底发生了什么  
- 也不再需要反复花时间把应用最基本的可运行状态重新修回来

### Testing

他们观察到的最后一种主要失败模式是：Claude 倾向于在**没有做好充分测试**的情况下，就把一个功能标记为完成。  
如果没有被明确要求，Claude 虽然会改代码，也可能会跑一些单元测试，或者对开发服务器执行 `curl` 命令，但它往往无法意识到：**这个功能其实并没有从端到端真正跑通。**

在构建 web app 的场景下，一旦被明确要求使用浏览器自动化工具，并且像真实用户那样去完成所有测试，Claude 在做端到端验证这件事上通常就表现得相当不错。

给 Claude 提供这类测试工具之后，它的表现会显著提升，因为 agent 能够识别并修复那些**仅靠读代码本身看不出来的 bug**。

当然，问题并没有完全消失。  
例如，Claude 的视觉能力和浏览器自动化工具本身都还有局限，这使得它仍然很难识别所有类型的 bug。  
举个例子，Claude 无法通过 Puppeteer MCP 看见浏览器原生的 alert 弹窗，因此那些依赖这类原生弹窗的功能，往往更容易残留 bug。

## Getting up to speed

在前面这些机制都具备之后，他们还要求每个 coding agent 在开始工作前，先跑一套“进入状态”的步骤。这些步骤虽然看起来很基础，但非常有帮助：

1. 先运行 `pwd`，确认自己当前工作的目录。你只能编辑这个目录里的文件。  
2. 阅读 git 日志和 progress file，快速理解最近做了什么。  
3. 阅读 feature list，挑出当前**最高优先级、且尚未完成**的功能点来处理。

这种做法还能帮 Claude 在每个 session 里节省一些 token，因为它不需要再临时自己摸索“这段代码到底该怎么测”。  
同时，他们还建议让 initializer agent 写一个 `init.sh` 脚本，这个脚本可以启动开发服务器，并且在开始实现新功能之前，先自动跑一遍基础的端到端测试。

在 claude.ai 克隆这个例子里，这意味着 agent 每次都会先启动本地开发服务器，然后使用 Puppeteer MCP 去做一套最基础的端到端流程：

- 新建一个聊天  
- 发送一条消息  
- 收到一个回复

这样一来，Claude 就能很快判断：当前应用是不是被留在了一个损坏状态。  
如果发现已有 bug，它会先把这些问题修掉。  
因为如果 agent 在一个已损坏的基础上直接继续实现新功能，往往只会让局面变得更糟。

基于以上做法，一个典型 session 往往会以如下流程开始：

- 先声明：我要先弄清当前项目状态  
- 跑 `pwd`  
- 读 `claude-progress.txt`  
- 读 `feature_list.json`  
- 再去看最近的 git log  
- 查有没有 `init.sh`，并启动开发服务器  
- 先验证基础功能仍然可用  
- 然后再决定下一个要做的新功能

文中还给了一张“常见失败模式与解决方案”对照表，核心意思如下：

- **过早宣布整个项目完成**  
  - 初始化阶段：建立 feature list  
  - 执行阶段：开头先读 feature list，并只选一个功能开始做

- **环境里留下 bug 或未记录进展**  
  - 初始化阶段：建立 git 仓库和 progress notes  
  - 执行阶段：开头读 progress notes 和 git log，并跑基础测试；结束时写 git commit 和进展更新

- **过早把功能标记为已完成**  
  - 初始化阶段：建立 feature list  
  - 执行阶段：必须先自验，只有认真测试过后才把 feature 标成 passing

- **agent 花时间摸索怎么启动项目**  
  - 初始化阶段：写 `init.sh`  
  - 执行阶段：session 开始时先读 `init.sh`

## Future work

这项研究展示了一组可能的方案：通过 long-running agent harness，让模型在多个 context window 之间持续做增量推进。  
但其中仍然有很多开放问题。

最值得注意的是，目前还不清楚：在跨上下文工作的场景下，**单个通用 coding agent** 是否就是最佳选择；还是说，通过**多 agent 架构**，能够得到更好的效果。  
看起来很合理的一种设想是：如果把测试、质量保证、代码清理等子任务交给专门的 agent，可能会在软件开发生命周期的不同环节里做得更好。

此外，这个 demo 主要是围绕**全栈 web app 开发**优化的。  
未来一个值得探索的方向是：把这些经验推广到其他领域。  
很可能，其中一部分甚至全部经验，也可以迁移到其他需要 long-running agent 的任务中，比如科学研究、金融建模等。

## 致谢

本文由 Justin Young 撰写。特别感谢 David Hershey、Prithvi Rajasakeran、Jeremy Hadfield、Naia Bouscal、Michael Tingley、Jesse Mu、Jake Eaton、Marius Buleandara、Maggie Vo、Pedram Navid、Nadine Yasser 和 Alex Notov 的贡献。

这项工作体现了 Anthropic 多个团队的共同努力，才使 Claude 能够安全地进行长周期的自治式软件工程，尤其是 code RL 和 Claude Code 团队。  
如果有人有兴趣参与类似工作，欢迎到 `anthropic.com/careers` 申请。

## 脚注

这里文中把它们称作不同的 agent，主要只是因为它们的初始用户 prompt 不同。  
除此之外，它们使用的 system prompt、工具集以及整体 harness 实际上是相同的。
