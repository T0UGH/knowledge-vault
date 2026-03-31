---
title: Daily Lane Report Feedback
date: 2026-03-31
tags:
  - daily-lane
  - feedback
  - report
status: open
source_repo: https://github.com/T0UGH/daily-lane
---

# Daily Lane Report Feedback

记录时间：2026-03-31
状态：**仅记录，不立即修改**

## 背景

贵平对当前 `daily-lane` 最终 report 提了两个小改进点，先记录到 Obsidian，后续再排期改。

---

## 反馈 1：最终 report 标题改成中文

### 当前问题

最终 report 内部标题里仍出现类似：

- `x-feed`
- `github-watch`

这种 lane/internal name。

### 期望改法

对最终给人看的 report，标题层应改成**中文可读标题**，不要直接暴露底层 lane 名称。

### 目标方向

可以理解为：

- lane 名保留在系统内部、目录、索引、调试信息中
- 面向用户的最终 report 标题，使用中文栏目名/中文段标题

### 验收口径

最终 report 中：

- 不再把 `x-feed`、`github-watch` 直接当作展示标题
- 改成中文标题
- 中文标题应能保留 lane 语义，但优先可读性

### 备注

后续实现时，需要统一梳理：

- lane internal name
- display title / report title
- 是否需要一份稳定 mapping 表

---

## 反馈 2：X 内容缺少点回 X 的原文链接

### 当前问题

report 里提到 X 上的内容时，没有方便点回原帖/原文的 X 链接。

### 期望改法

凡是来自 X 的条目，在最终 report 中应提供**可直接点回 X 原帖的链接**。

### 验收口径

对于 report 中来自 X 的内容：

- 用户可以直接点开对应 X 链接
- 链接应尽量贴近具体原帖/原文，而不是只给账号主页或模糊来源
- 若正文中不适合直插，可至少在该条目的 sources / 链接区域中明确给出

### 备注

后续实现时，需要一起检查：

- `x-feed`
- `x-following`
- 中间 signal/index 到最终 report 的链接保留链路是否完整
- 是否存在 signal 有 URL、但 report 渲染时被丢掉的问题

---

## 下一步建议

后续真正修改时，建议按这个顺序处理：

1. 先确认最终 report 的中文标题映射策略
2. 再排查 X signal -> draft -> report 的原文 URL 透传链路
3. 做一次真实生成验证，确认标题与链接都出现在最终 report 中

---

## 一句话总结

这次先记下两件事，不急着改：

1. **最终 report 标题改成中文，不直接露出 `x-feed` / `github-watch` 这类内部名**
2. **X 条目要能直接点回 X 原帖链接**
