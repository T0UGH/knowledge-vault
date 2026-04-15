# 2026-04-15 日报 Reddit / X 风格调优记录

- 日期：2026-04-15
- 主题：把 `daily-report-master-agent` 的 Reddit / X reader-facing 输出从“能跑”推进到“更像真正日报”
- 状态：已完成第一轮可用升级；明天先跑定时任务，再按真实结果继续修

## 背景

这次调优不是从零开始做日报，而是在现有 `daily-report-master-agent` / `signals-engine` 基础上，解决两个具体问题：

1. **Reddit 候选过少 / 过于单一路径**
   - 扩容前，`reddit-watch` 候选太少
   - 扩容后，真实数据一度到 `112` 条，但 `daily-report-master-agent` 旧逻辑仍可能只选出 `1` 条
2. **Reader-facing 文案太粗**
   - 常退回类似 `原文围绕…展开，具体变化见来源。`
   - 用户明确不接受这类空话，希望每条至少三句，讲明白“这是什么 / 关键变化 / 为什么值得看”

## 本次达成的效果

### 1. Reddit source 恢复可抓 + 扩容到 30
- 配置把 `reddit-watch` 从 `max_threads: 5 / max_per_query: 3` 提到 `30 / 30`
- 真实跑通后，`2026-04-15` 的 Reddit signals 从 `12` 条扩大到 `112` 条

#### 根因排查结论
Reddit public source 的 403 不是 `limit=30` 导致，而是请求头组合问题：
- 旧逻辑：
  - `User-Agent: signals-engine/0.1 reddit-watch`
  - `Accept: application/json`
- 在当前环境下，这个组合会稳定触发 `403`
- 最小修复：移除显式 `Accept: application/json`

### 2. Reddit 选择层改成双池
在 `daily-report-master-agent` 中，将 `reddit-watch` 的选择从单一路径改成：
- `热帖`（heat）
- `原声`（voice）
- 再做余量回填

同时保留了：
- 跨日前一天去重
- 基本的多样性控制

#### 结果
- 之前：112 条候选也可能只选出 1 条
- 现在：`per_lane_limit=10` 时能稳定选出 10 条，并且混合了热帖和原声

### 3. Reddit 文案升级到三句式
已把多条关键 Reddit case 改成：
- 至少三句
- 有具体信息
- 不再用 `原文围绕…展开` 这类套话

Feishu 临时预览：
- v1（双池刚落地）：https://feishu.cn/docx/XR11dx8uKos6xaxVKuvc3jHEn0c
- v2（三句式 + 去套话）：https://feishu.cn/docx/SLigdPhiKoLVi0xluwvcdrebnnb

### 4. X 两条线接入相同标准
对：
- `x-feed`
- `x-following`

也做了第一轮升级：
- 重点坏例子不再退回空话
- 像 `@trq212 #40`、`@icanvardar #73`、`@aiedge_` 已经能输出三句式中文解释

Feishu 临时预览：
- X 两条线预览：https://feishu.cn/docx/KVqddz4itoffcax3dn5ch32VnJM

## 用户确认的输出标准
后续 Reddit / X / 社区类输出，默认按以下标准：

### 单条标准
每条至少三句，讲清：
1. 这是什么
2. 关键变化 / 做法 / 卡点
3. 为什么值得看 / 讨论真正落点是什么

### 禁止项
禁止使用：
- `原文围绕…展开，具体变化见来源。`
- 只有抽象态度词，没有源信息的句子
- 只给一句粗摘要，读完仍不知道发生了什么

### 结构标准
优先采用接近日报的分层方式：
- 热帖
- 原声
- 值得一看（后续可继续补）

## 当前仍存在的不足
虽然已经明显变好，但还不是最终版。

### Reddit
- 结构已经对了
- 文案可读性已明显提升
- 但仍属于“规则驱动的日报化改写”，后续还可继续细化层次和栏目组织

### X
- 最差的空话 fallback 已经被打掉一批
- 但还有部分条目仍偏“原文截断 / 转述感太重”
- 特别是中文长帖、长线程摘要、中文营销风格内容，还没有完全收成稳定的日报语言

## 为什么这篇记录重要
这不是单次任务日志，而是**后面继续调日报质量时的参考基线**：

1. 已经知道 Reddit 候选扩容后会发生什么
2. 已经验证“热帖 + 原声”对 Reddit 是有效结构
3. 已经确认用户接受的文案下限：三句式、禁套话
4. 已经把同一标准推广到 X 两条线
5. 明天可以直接用真实定时任务产出做第二轮修正，而不用再重新对齐标准

## 建议的下一步
1. **明天先看定时任务真实产出**
   - 不急着今晚继续过度微调
   - 先看真实跑出来的 Reddit / X 两条线整体效果
2. **按真实成品再做第二轮修正**
   - 重点盯 X 里仍然偏原文片段残留的条目
   - 再考虑是否把 Reddit/X 都进一步升级成真正分段的栏目结构
3. **后续把这套标准继续沉淀成更明确的日报风格约束**
   - 让未来调日报不是靠临时记忆，而是靠固定规则 / 测试 / 参考样例

## 相关提交
### daily-report-master-agent
- `c09f078` — `feat: upgrade reddit and x reader-facing report quality`

### knowledge-vault
- `4f5998b` — `docs: record reddit dual-pool preview standard`

本记录是在此基础上补充的更完整过程与经验总结。