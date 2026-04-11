---
title: fireworks-tech-graph 快速扫眼
status: draft
created: 2026-04-11
updated: 2026-04-11
tags:
  - projects
  - diagram
  - svg
  - claude-skill
  - agent
---

# fireworks-tech-graph 快速扫眼

- 仓库：`https://github.com/yizhiyanhua-ai/fireworks-tech-graph`
- 本地位置：`~/workspace/fireworks-tech-graph`
- 扫描时间：2026-04-11

## 一句话判断

这是一个**偏 Claude Code skill 形态的技术画图仓库**：把自然语言描述转换成技术图，核心产物是 **SVG + 1920px PNG**，重点不是通用绘图 DSL，而是**面向 AI/Agent 场景的高质量出图模板**。

## 我快速看到的东西

### 1. 它不是一个重工程应用，更像“技能包 + 规范库”
仓库非常轻：

- `SKILL.md`：主技能说明与执行流程
- `README.md` / `README.zh.md`：项目定位、使用方式、示例
- `references/`：5 套视觉风格与图标/规范参考
- `assets/samples/`：样例图

也就是说，它的核心价值更像：

> 用一套固定 workflow + SVG 约定 + 风格参考，把“AI 画技术图”这件事标准化。

### 2. 它的目标用户很明确：写 AI/Agent 内容的人
README 里强调的不是通用组织架构图，而是这些高频场景：

- RAG
- Agentic Search
- Mem0
- Multi-Agent
- Tool Call Flow
- Agent Memory Types

这说明它不是在和 Mermaid 全面竞争，而是在打一个更窄、更实用的切口：

> **给 AI/Agent 写作者、研究者、内容作者快速出一张能发的技术图。**

### 3. 它真正有价值的不是“能生成 SVG”，而是“内建了语义词汇”
`SKILL.md` 里最值钱的是这几层约定：

- Diagram Types（架构图、数据流图、流程图、Agent 图、Memory 图、时序图、对比图、脑图）
- Shape Vocabulary（LLM、Agent、Memory、Vector Store、Graph DB、Tool、API 等对象应该长什么样）
- Arrow Semantics（读、写、异步、控制流、反馈环分别用什么颜色/线型）
- Layout Principles（分层、对齐、泳道、间距）

所以它本质上不是“随便生成一张图”，而是在试图把：

> **技术图里的视觉语义规范，先编码成一套 promptable system。**

### 4. 它很适合内容生产，不一定适合复杂产品化
优点：

- 上手成本低
- 对 AI/Agent 题材友好
- 5 种风格够覆盖文章、README、Wiki、演示稿
- SVG + PNG 双输出，适合继续改和直接发

边界：

- 目前看更像静态生成 skill，不像完整可视化产品
- 对超复杂图、多人协作编辑、持续维护图谱，能力可能有限
- 它依赖 `rsvg-convert`，所以更适合本地/CLI 工作流，不是纯在线 SaaS 路线

## 当前结构印象

```text
fireworks-tech-graph/
├── SKILL.md
├── README.md / README.zh.md
├── references/
│   ├── style-1-flat-icon.md
│   ├── style-2-dark-terminal.md
│   ├── style-3-blueprint.md
│   ├── style-4-notion-clean.md
│   ├── style-5-glassmorphism.md
│   └── icons.md
└── assets/samples/
```

## 我当前的使用判断

如果后面要做：

- AI/Agent 相关文章配图
- 技术手册中的结构图
- README 的架构图 / 流程图
- 飞书文档或公众号内容里的快速技术图

这个仓库值得继续试。

如果目标是：

- 做一个可交互的图产品
- 做大型复杂系统图管理
- 团队协作维护一张长期演进图谱

那它更像一个**出图引擎雏形**，不是最终形态。

## 我给它的定位

一句更狠的定位可以写成：

> `fireworks-tech-graph` 不是在造新的绘图语言，而是在把 AI/Agent 技术图的常见语义、形状、箭头和风格模板，打包成一个可直接调用的 Claude Code 技能。

## 后续如果要继续看，优先看三件事

1. **实际出图质量**：随便拿 2~3 个真实题目跑一下，看图是不是能直接发。
2. **可修改性**：生成后的 SVG 二次编辑成本高不高。
3. **风格扩展性**：新增一种风格或新增一类图，改动成本大不大。
