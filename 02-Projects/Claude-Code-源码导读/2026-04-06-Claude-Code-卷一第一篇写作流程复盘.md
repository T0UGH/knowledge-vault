---
title: Claude Code 卷一第一篇写作流程复盘
date: 2026-04-06
tags:
  - Claude Code
  - 源码导读
  - 写作流程
  - reader-friction-reviewer
  - workflow
---

# Claude Code 卷一第一篇写作流程复盘

## 结论先说

这次《卷一 01｜Claude Code 到底是什么系统》的写作，不是“一次写完”，而是一个比较清晰的多阶段流程：

1. **先按卷一新定位起稿**：把旧的工具/入口文章，重写成“系统全景导论”的开篇。
2. **再做解释写作向 review**：重点看主判断是否立住、五层台阶是否成立、有没有作者视角过重的问题。
3. **再补一个新的“读者视角审稿”能力**：专门模拟“用过 Claude Code，但不懂原理的工程师读者”，抓卡点、废话感、重复解释和读后残留。
4. **最后再收成定稿**：把 `runtime` 从口号压成职责集合，同时继续砍低增量重复。

也就是说，这次不是单纯“写文章”，而是顺手把一套更稳的技术写作工作流跑出来了。

---

## 这次中间实际经历了哪些步骤

## Step 0：先确认卷一第 1 篇的任务变了

这篇原来挂在旧文件名 `01-tool-overview-and-bash-entry.md` 下，但卷一新定位已经变成：

> **不是从工具或入口讲起，而是先回答：Claude Code 到底是什么系统。**

所以第 1 步不是修旧稿，而是**换开篇任务**：

- 从“工具入口文”改成“系统总图文”
- 从“某个能力怎么用”改成“读者先立整张地图”
- 从“目录/源码导向”改成“认知坡度导向”

这一步对应的第一次正式起稿提交是：

- `6e220eb` — `rewrite volume one opening chapter as system overview`

---

## Step 1：第一轮起稿——先把大判断立住

第一轮起稿的核心目标不是细修句子，而是先把卷一第一篇该有的骨架打出来。

当时主要做了几件事：

- 把主问题定成：**Claude Code 到底是什么系统**
- 把核心判断定成：**Claude Code 不是聊天壳，而是 agent runtime**
- 加一张系统总图 Mermaid
- 拉出 5 层结构：
  - 交互入口层
  - 主循环 / runtime 编排层
  - 执行能力层
  - 上下文与状态层
  - 扩展能力层
- 让卷一后面的 5 篇自然挂在这张地图后面

这一步的意义是：

> 先把“开篇应该讲什么”定住，而不是先追求语言上的精细。

---

## Step 2：第二轮 review——从解释写作角度收紧

起稿出来之后，没有直接当成成稿，而是先从解释写作角度 review。

这轮主要抓到的问题是：

- 开头虽然方向对，但“定义”还不够狠
- “为什么会看浅”写得略长
- 五层结构更像作者在分层，而不是读者在踩台阶
- “真正难的地方”那一节有点和前文重复
- 结尾的卷内导航感还可以更强

于是做了第二轮收紧，重点是：

- 把开头改得更像定义式入口
- 压缩“不要把它看浅”的解释
- 让五层结构更像认知台阶
- 让结尾更像导航而不是解释

对应提交：

- `bb23c3e` — `tighten volume one opening chapter framing`
- `704a0ed` — `strengthen volume one opening chapter structure`

这一步的意义是：

> 先把“解释结构”收顺，让它像一篇真正的开篇正文，而不是一个总论草稿。

---

## Step 3：飞书外部阅读感检查

在中途没有只靠本地看，而是把稿子上传成临时飞书文档，直接看阅读感。

这次至少用了两个临时飞书版本：

- `https://feishu.cn/docx/Mra7dJMh2oSDRWxgSU5c7NT0n3c`
- `https://feishu.cn/docx/FuAldnL1VopI7tx5UOocePCVnHb`

中间一个很实际的点是：

- Mermaid 图导入是否正常
- 整体块结构是否在飞书里仍然顺
- 文章在“聊天窗口里看起来顺”和“文档里读起来顺”是不是一回事

这一步提醒了一件事：

> 技术文章最终不是只在 terminal 里成立，而是要在真实阅读容器里成立。

---

## Step 4：意识到还缺一个“读者视角”能力

做到这里以后，一个很明显的问题冒出来了：

- 解释结构已经越来越对
- 但还是会感觉某些段落“逻辑没错，读者却可能觉得有废话”

这时发现：

- `claude-code-source-anchor` 负责源码证据
- `tech-explainer-coach` 负责解释写作
- `claude-guidebook-editor` 负责项目边界编辑

但还缺一层：

> **站在真实读者那边，专门抓“哪里会卡、哪里像废话、读完到底留下什么”的能力。**

于是没有继续硬改文章，而是先把这个能力抽象成新 skill。

---

## Step 5：把“读者摩擦层审稿”单独做成新 skill

接下来先做了 brainstorming，明确这个 skill 的边界和默认画像。

最后定下来的核心定义是：

> **`reader-friction-reviewer`：模拟“用过 Claude Code、但不懂内部原理的工程师读者”，识别阅读阻力、废话感、重复解释、读后残留，并给出删改建议与少量示例改写。**

它的边界也定得很清楚：

- 不做源码证据校验
- 不做项目边界编辑
- 不整篇重写
- 只做“读者摩擦层审稿”

为了把这个能力落稳，中间又走了完整一套流程：

1. 写 design spec
2. 写 implementation plan
3. 写 skill 本体和 reference rules
4. 打包并验证 skill

对应提交：

- `02b5885` — `add reader friction reviewer design spec`
- `4783e88` — `add reader friction reviewer implementation plan`
- `d68641c` — `create reader friction reviewer skill`

这一步的意义不是“顺手做了个 skill”，而是：

> 把原来靠直觉感觉到的“读者会烦”这件事，沉淀成了一个可以复用的方法层。

---

## Step 6：用匿名 subagent + reader-friction-reviewer 真审一遍

新 skill 做出来以后，没有停在设计层，而是立刻拿匿名 subagent 去审这篇。

这轮审稿抓到的重点非常集中：

- `runtime` 这个词虽然已经立住，但对目标读者来说还偏抽象
- 系统总图和图后文字之间的咬合还不够紧
- 五层台阶之间的差异感不够强
- 中后段还有重复强调中心判断的问题
- 某些部分更像目录说明而不是阅读推进

这里最关键的 insight 是：

> 读者会留下“正确口号 + 正确框架”，但还不一定留下“为什么这个框架成立”。

这句话很值，因为它直接指出：

- 问题不是方向错
- 而是“runtime”还没从口号压成职责集合

---

## Step 7：最后一轮定稿——把 runtime 变成职责集合

拿到 reader-friction-reviewer 的反馈之后，又做了最后一轮真正有决定性的修改。

这轮核心就两件事：

### 1. 把 runtime 从口号压成职责集合

不再只说：

- Claude Code 是 agent runtime

而是直接落成 5 个职责：

- 接住用户输入
- 组织 agent turn
- 调度执行能力
- 维护上下文与状态
- 接入扩展能力

这样读者不会只记住一个抽象名词，而会记住：

> runtime 到底具体负责哪些组织工作。

### 2. 继续砍低增量重复

主要砍掉了：

- “不是 X，而是 runtime”的反复强调
- 独立成节、但和前文同义的重复总结
- 过长的卷内目录式导流
- 偏作者视角的自我说明
- 第一篇里过早抛出的术语堆

对应最终接受版提交：

- `843668d` — `sharpen volume one opening runtime responsibilities`

这一步之后，这篇才真正从“对的总论稿”变成了“可以接受的卷一第一篇”。

---

## 这次流程最后沉淀出了什么

## 1. 一条更稳的文章生产顺序

这次实际跑出来的顺序大概是：

1. **先定卷内角色**：这篇在整卷里到底承担什么任务
2. **再起系统骨架**：主问题、核心判断、总图、台阶
3. **再做解释写作收紧**：让它像正文，而不是草稿
4. **再做真实阅读容器检查**：飞书文档里看阅读感
5. **再做读者摩擦层审稿**：抓卡点、废话感、读后残留
6. **最后做定稿收束**：把口号压成职责、砍掉低增量重复

这比“写完就改”“改到自己满意”为止要稳得多。

---

## 2. 一个新的独立能力层：读者摩擦层审稿

以前技术写作 review 更多是：

- 对不对
- 清不清楚
- 结构顺不顺

这次补出来的是第四层：

- **读者会不会烦，会不会滑走，会不会只记住口号**

这一层非常适合导论文、系统解释文、源码导读文。

---

## 3. 一个很具体的写作经验

对于这类“系统总论文章”，一个很容易掉进去的坑是：

> 立住了正确判断，但没有把这个判断压成读者能复述的职责集合。

结果就是：

- 作者觉得已经讲清了
- 读者也觉得自己好像懂了
- 但读完之后，脑子里只剩一句口号

这次最后能收住，关键就在于把 `runtime` 从抽象判断，压成了一个更可复述的职责组。

---

## 当前可复用的模板

如果后面继续写卷一 02、03、04，这次流程里最值得复用的，不是具体句子，而是下面这个工作流：

1. **先明确该篇在整卷中的功能**
2. **先起骨架，不先抠句子**
3. **先做解释写作向 review**
4. **再做读者视角审稿**
5. **最后只盯两件事做定稿**：
   - 核心判断有没有压成职责 / 机制 / 因果
   - 低增量重复有没有继续残留

这套流程对“系统导论类文章”应该是通用的。

---

## 相关文件与提交

### 文章文件
- `/Users/haha/.openclaw/workspace/claude-code-source-guide/docs/guidebook/volume-1/01-tool-overview-and-bash-entry.md`

### 关键提交
- `6e220eb` — `rewrite volume one opening chapter as system overview`
- `bb23c3e` — `tighten volume one opening chapter framing`
- `704a0ed` — `strengthen volume one opening chapter structure`
- `843668d` — `sharpen volume one opening runtime responsibilities`

### 新 skill 相关提交
- `02b5885` — `add reader friction reviewer design spec`
- `4783e88` — `add reader friction reviewer implementation plan`
- `d68641c` — `create reader friction reviewer skill`

### 飞书临时文档
- `https://feishu.cn/docx/Mra7dJMh2oSDRWxgSU5c7NT0n3c`
- `https://feishu.cn/docx/FuAldnL1VopI7tx5UOocePCVnHb`

---

## 一句话总结

这次《Claude Code 到底是什么系统》的写作流程，本质上是：

> **先把文章写对，再把文章写顺，最后再用读者视角把“口号”压成“能留下的系统模型”。**

而 `reader-friction-reviewer` 的出现，就是这条流程里新长出来的关键一层。
