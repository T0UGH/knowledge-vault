---
title: Obsidian Vault 整理评估（2026-04-06）
date: 2026-04-06
tags:
  - obsidian
  - vault
  - cleanup
  - knowledge-vault
status: active
aliases:
  - Vault 整理评估
  - Obsidian 整理清单
---

# Obsidian Vault 整理评估（2026-04-06）

这篇笔记记录本轮对 `knowledge-vault` 的整体体检结论，目标不是一次性大扫除，而是建立一个**可分批推进的整理基线**。

> [!important]
> 这次的判断重点不是“内容太多”，而是**目录边界、主题归属、创作工作流分层**还没完全收死。

---

## 当前总体判断

Vault 现在已经过了“纯堆积”的阶段，主干结构是有的，但处于一个典型的第二阶段问题：

> **主结构已经建立，但 Projects / Research / Reference / Posts / 创作工作区 之间的边界还不够硬。**

这意味着现在最需要的不是“全量搬家”，而是：

1. 收紧几个高频目录的职责
2. 减少重复正文
3. 为高密度主题补 index
4. 给后续落笔建立更稳定的默认规则

---

## 本轮已经完成的整理

### AGM 线已完成一轮收口

已经完成：

- 合并重复目录：`Agent-Git-Mail` → `Agent Git Mail`
- 把 root 漏文件归位
- 清理 `公众号创作` 下 4 份与 AGM 完全重复的正文
- 用索引笔记替代重复副本

这意味着 AGM 这一条主线，目前已经显著比之前整洁。

---

## 现在剩下最值得讨论的区域

## 1. 内容创作体系边界还不够硬

涉及目录：

- `02-Projects/内容创作`
- `02-Projects/公众号创作`
- `07-Posts/WeChat`

### 当前判断

这三层本来可以形成一个完整工作流：

- `内容创作`：内容项目工作区 / source / 多平台拆分
- `公众号创作`：公众号渠道草稿 / 选题 / 改写稿
- `07-Posts/WeChat`：最终稿 / 准发布稿 / 已发布稿

但目前还缺一条明确规则，导致后续很容易继续漂移：

- 选题思路写到最终稿目录
- 已成稿还留在公众号创作目录
- 项目思考和渠道草稿混放

### 当前建议

后续需要把这三层的规则明确写成：

- **项目过程** → `02-Projects/内容创作`
- **渠道草稿** → `02-Projects/公众号创作`
- **最终发布稿** → `07-Posts/WeChat`

> [!note]
> 这是当前除 AGM 之外，**优先级最高** 的整理议题。

---

## 2. Claude Code 主题横跨 Project / Research，边界不够清楚

涉及目录：

- `02-Projects/Claude-Code`
- `03-Research/Claude Code`

### 当前现状

- `02-Projects/Claude-Code`：3 篇
- `03-Research/Claude Code`：101 篇

### 当前判断

现在 `Claude Code` 这个主题本质上已经是一个**研究主题**，不是普通项目目录。

问题不在于“有两个目录”，而在于：

> `02-Projects/Claude-Code` 现在像一个壳子，但职责没有被明确下来。

### 当前建议

后续需要收口为：

- `02-Projects/Claude-Code`：项目入口 / 行动页 / 少量推进型文档
- `03-Research/Claude Code`：研究主库 / 源码导读 / 专题调研 / 方法论

---

## 3. Reference 目录过胖，而且几乎被 OpenClaw Docs 吞没

涉及目录：

- `06-Reference`
- `06-Reference/OpenClaw Docs`

### 当前现状

- `06-Reference`：366 篇
- 其中 `OpenClaw Docs`：361 篇

### 当前判断

现在 `06-Reference` 的语义已经有点失真，实际上几乎等于：

> OpenClaw 本地文档镜像区

这本身不是错，但会带来两个问题：

1. `Reference` 的名字会误导后续放置习惯
2. 其他参考内容进入这里时，容易被单一 docs 镜像淹没

### 当前建议

短期不建议大搬家，但建议后续明确一条说明：

- `06-Reference` 当前主要承载 OpenClaw docs mirror
- 不是所有“值得存档的资料”都应该默认放进 Reference

后续需要补一条准入规则：

- **稳定事实 / 手册 / 文档镜像** → `Reference`
- **阶段性判断 / 调研结论 / 方案对比** → `Research` 或 `Projects`

---

## 4. Root 基本干净，但还有一个孤儿文件

当前 root 只剩：

- `README.md`
- `未命名.canvas`

### 当前判断

- `README.md` 正常
- `未命名.canvas` 明显是临时产物，没有归位

### 当前建议

后续需要判断：

- 如果它有用：改名并移入对应目录
- 如果没用：删除

这件事优先级不高，但它是一个典型“临时产物没有归宿”的信号。

---

## 5. 缺少真正可用的 index 页

当前最值得补 index 的地方：

- `02-Projects/Agent Git Mail`
- `02-Projects/OpenClaw`
- `02-Projects/内容创作`
- `03-Research/Claude Code`
- `03-Research/AI`
- `03-Research/Agent-Systems`

### 当前判断

当笔记数量到这个规模后，目录本身已经不够了。

后续需要的是：

> **少量人工维护的入口页，把高价值内容和长期结构串起来。**

否则后面会越来越依赖搜索，目录会继续失去组织作用。

---

## 当前优先级排序

### P1：最值得先讨论

1. **内容创作体系边界**
2. **给 `Agent Git Mail` 做 index**
3. **处理 `未命名.canvas`**

### P2：下一轮值得收口

4. **Claude Code 的 Project / Research 边界**
5. **给 `内容创作` 做 index**

### P3：系统化优化

6. **给 `06-Reference` 补边界声明**
7. **给几个高密度 Research 目录补 index**

---

## 一句话结论

如果只问“除了 AGM 之外，哪里最乱”：

1. **第一名：内容创作 / 公众号创作 / 07-Posts/WeChat**
2. **第二名：Claude Code 在 Project / Research 之间的角色不够明确**
3. **第三名：Reference 目录规模已经过大，但语义还没补齐**

---

## 后续讨论顺序（建议）

建议不要再大扫除式乱动，而是按下面顺序一个个讨论：

1. 内容创作体系
2. AGM index
3. Claude Code 边界
4. Reference 边界说明

> [!success]
> 现在最重要的不是“一次整理完”，而是把**后续默认落笔规则**先建立起来。那样整理才不会反复回潮。
