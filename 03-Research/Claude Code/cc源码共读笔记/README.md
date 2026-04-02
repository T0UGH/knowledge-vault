---
title: cc源码共读笔记｜系列索引
date: 2026-04-01
tags:
  - claude-code
  - source-reading
  - series
  - research
status: in-progress
source_repo: https://github.com/T0UGH/cc
---

# cc源码共读笔记｜系列索引

这组笔记从原先散落在 `06-Reference/Claude Code/` 的几篇 skill 相关文档，重组为 `03-Research/Claude Code/cc源码共读笔记/` 下的连续系列。

定位调整：

- **不是纯 reference 速查库**
- **而是带阅读路径、源码核对、讨论推进和阶段结论的共读研究笔记**

## 当前目录

- `00` 学习路线
- `01` Skill 总图
- `02` Skill frontmatter 字段速查（humanized）
- `03` Skill / Fork / Agent / Review
- `04` 环境变量有效性校验
- `05` Tool 总图与 BashTool 入口
- `06` Claude Code tools 总览
- `07` Tool 体系总览
- `08` 主循环里 tool 怎么被接住
- `09` 从 query 到 tool-call 的连接处
- `10` BashTool 不是跑个 shell 这么简单
- `11` FileReadTool 统一读取入口
- `12` FileEditTool 是受控修改原语
- `13` FileWriteTool 是整文件覆盖原语
- `14` GrepTool 是结构化内容搜索原语
- `15` AgentTool 不是再开一个 agent 这么简单
- `16` SkillTool 是把 skill 接进 runtime 的桥
- `17` ToolSearchTool 是工具发现层
- `18` 主循环在编排一次完整 agent 回合
- `19` runAgent 是把 agent 组装成可运行体的装配线
- `20` forkSubagent 是在同一上下文里分叉出 worker

## 阅读顺序建议

1. 先看 `00`，建立整体学习顺序
2. 再看 `01`，建立 skill 的总体心智模型
3. 然后看 `02`，把 frontmatter 细节补齐
4. 再看 `03`，理解 skill、fork、agent、subagent 的边界
5. 最后看 `04`，作为源码核对类专题示例

## 后续扩展建议

后面继续沿这个系列写时，统一命名：

- `cc源码共读笔记 06｜...`
- `cc源码共读笔记 07｜...`

这样后面关于 tool、session、权限、遥测、MCP、bridge 的内容都可以自然接进来。
