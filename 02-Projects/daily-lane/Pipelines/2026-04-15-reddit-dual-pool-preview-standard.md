# 2026-04-15 Reddit 双池预览标准（临时效果沉淀）

- 时间：2026-04-15
- 目标：把 `daily-report-master-agent` 的 Reddit 输出从“单一路径 + 一句粗摘要”升级成更像日报的 reader-facing 结构。
- 适用范围：先用于 `reddit-watch`，后续扩展到 `x-feed` / `x-following` 等社区型来源。

## 本次确认的标准

### 结构标准
优先采用类似下面的分层思路：

1. 热帖
2. 原声
3. 值得一看（可后续扩展）

当前已先落地 `热帖 / 原声` 双池选择；“值得一看”还未单独拆段。

### 单条文案标准
每条至少三句，必须讲清：

1. 这是什么
2. 关键变化 / 做法 / 卡点是什么
3. 为什么值得看 / 讨论真正落点是什么

### 禁止项
禁止再出现这类空话：

- `原文围绕…展开，具体变化见来源。`
- 只说“值得关注 / 更清楚了 / 更像工作流了”但没有源信息
- 只有一句很粗的抽象总结，读者看不懂到底发生了什么

## 这轮实际效果

### Feishu 临时预览
- v1（双池刚落地，文案仍粗）：https://feishu.cn/docx/XR11dx8uKos6xaxVKuvc3jHEn0c
- v2（每条至少三句、去掉套话）：https://feishu.cn/docx/SLigdPhiKoLVi0xluwvcdrebnnb

### 关键变化

#### 1. Reddit 候选池不再被压成 1 条
- 真实数据：`~/.daily-lane-data/signals/reddit-watch/2026-04-15/signals`
- 扩容后有 112 条候选
- 旧逻辑在 `per_lane_limit=10` 时只会选出 1 条
- 新逻辑改成 `热帖 + 原声 + 余量回填` 后，能稳定选出 10 条

#### 2. 文案从一句空话升级成三句具体介绍
例如：

- `Claude version of Openclaw coming soon?`
  - 不是发布公告
  - 是在猜 Anthropic 会不会把 Claude Code 能力下放
  - 文中举了 `Cowork`、`MCP`、`skills`、`/monitor`

- `Built a WhatsApp AI assistant with Claude Code as an OpenClaw alternative`
  - 讲的是 WhatsApp + Claude Code 的 agent 方案
  - 作者不用 OpenClaw 的原因是安全模型不放心
  - 还明确写了成本与信任选择：`Claude Max` + Anthropic runtime

## 为什么这次效果可以作为后续参考

因为它解决了两个实际问题：

1. **选什么**
   - Reddit 不再只剩单一路径筛出来的 1 条
   - 有“热帖”和“原声”两种信息层

2. **怎么写**
   - 不再退回抽象套话
   - 每条都有足够信息帮助读者快速理解帖子在说什么

## 后续应用建议

### 1. 直接推广到 X 两条线
后续 `x-feed` / `x-following` 也应默认采用同一标准：

- 每条至少三句
- 不准用套话
- 优先让读者看完就知道：这是什么 / 关键变化 / 为什么值得看

### 2. 可继续升级的方向
- 把 `热帖 / 原声` 进一步渲染成分段，而不是一个 section 里的混合 bullets
- 加 `值得一看`
- 后续再考虑做“社区温度计”这类更高层摘要

## 备注
这篇笔记不是最终产品 spec，而是“用户已确认可接受的 reader-facing 效果参考”。后续做 X / Reddit / 社区类日报输出时，应优先对齐这里的标准与示例链接。
