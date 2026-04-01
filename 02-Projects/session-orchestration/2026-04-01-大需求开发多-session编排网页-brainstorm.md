---
tags:
  - session-orchestration
  - brainstorm
  - web-design
  - agent-workflow
created: 2026-04-01
status: brainstorming
---

# 2026-04-01 大需求开发多-session编排网页 brainstorm

## 目标
把一张手绘的大需求开发流程图，整理成一页**精美、可分享、展示型**网页。

这页网页不是普通线性流程图，也不是抽象方法论海报，而是对一次真实实践的可视化表达。

## 已确认方向

### 页面定位
- 展示型网页
- 专业系统图风
- 不保留手绘稿痕迹
- 主流程清楚 + 每阶段一句解释

### 语义定位
这张图表达的不是泛泛的 agent 编排，而是：

- 一个大需求会被拆成很多更细的 **session** 来推进
- 这些 session 可能分别由不同执行体承担：
  - Claude Code
  - Codex
  - 其他 agent / 执行体
- 人在本次实践中是主控
- 这套做法可以理解为一个**强化版 superpower**

### 拆分方式
- 主拆分维度：**按阶段**
- 但不是“一阶段一个 session”
- 粒度更细：一个阶段内部还会拆出多个 session

### 为什么拆多个 session
主要原因有两个：
- **并行推进**
- **上下文隔离**

## 网页主叙事
不是“抽象方法论发布”，而是：

> 这次真实实践里，一个大需求如何被拆成多个 session，由不同执行体在不同阶段中并行推进、循环回流，最终在人的主控下逐步收敛。

## 视觉主角
- **session 是主角**
- 阶段不是主角，只是对 session 的组织框架

这意味着页面不应做成普通“几个阶段串起来”的流程图，而应更像：

- 一个 **session orchestration map**
- 阶段作为浅层分区
- session 节点作为主要视觉元素

## session 之间的关键关系
这页图里最重要的不是单一顺序，而是：

- 并行
- 回环 / 循环
- 汇总
- 再进入下一阶段

也就是说，底层结构更像一个：

> human-controlled multi-session execution graph

## session 节点需要显示的信息
每个 session 节点显示：
- session 名称
- 执行体
- 产出物

## 页面定位：实践流程图
这一页更偏：
- **真实实践流程图**

而不是：
- 抽象方法论发布

所以页面气质应当：
- 真实
- 专业
- 有方法感
- 但不装成放之四海皆准的理论体系

## 选定的表现方案
最终选定：**方案 B**

### 方案 B：以 session 为主角的 orchestration map
- 页面中心不是阶段，而是大量 session 节点
- 阶段只作为浅层分区 / 泳道
- 重点突出 session 之间的依赖、并行、汇总、回流
- 人主控是整个网络的上层控制者，但不是唯一视觉中心

## 建议中的页面结构

### 1. 顶部
- 标题
- 一句副标题，点明：
  - 人是主控
  - session 是基本推进单元
  - 执行体可以是 Claude Code / Codex / other

### 2. 主图区域
- 按阶段进行浅层分区
- 每个分区里包含多个 session 节点
- 节点展示：
  - 名称
  - 执行体
  - 产出物
- 连线表达：
  - 并行展开
  - 汇总
  - 回流
  - 跨阶段推进

### 3. 主控说明
单独说明：
- 本次实践里，人是主控
- 人负责：
  - 定目标
  - 判断是否继续拆分
  - 在不同 session 间切换
  - 接收结果并决定下一轮编排

### 4. 页尾说明
简短补一句：
- 这不是“一阶段一个 session”
- 而是“按阶段组织、按 session 细拆”
- 细拆的主要原因：并行推进、上下文隔离

## 视觉语言建议
适合：
- 深色或浅灰底
- 系统图 / 架构图式细线框
- 克制的高亮色
- 干净的箭头与连接线
- 强结构感卡片

不适合：
- 过强的营销页渐变
- 过多发光特效
- 太像产品 landing page
- 太像 PPT 流程框图

目标是：

> 像一张高级的系统编排图网页，而不是营销页。

## 执行体视觉编码建议
建议用颜色或标识区分：
- Claude Code
- Codex
- Other / custom agent
- Human control

以便让人一眼看出这是多执行体协同，而不是单 agent 流程。

## 节点命名风格
已选定：**偏真实工作名（B）**

即节点名称不只用抽象职责名，而应尽量贴近真实实践，例如：
- 拆 PRD
- 出实现方案
- Codex 写 patch
- Claude Code review
- 回归验证

这样更符合“真实实践流程图”的定位。

## 已聊清楚的真实流程（补充）

### Step 1：一句话需求 → 初版 design
- 起点只是一句很简短的需求
- 第一反应不是直接实现，而是先把需求聊成 design
- 这里使用的是：
  - Claude Code
  - Opus 模型
  - superpower 的 brainstorm 流程
- 通过多轮 chat，把一句话需求逐步收敛成一个**初版 design**

这一阶段的关键不是产出代码，而是把模糊目标变成一个可继续拆解、可继续评审的设计草案。

### Step 2：围绕初版 design，多路并行找问题
拿到初版 design 之后，不是直接进入实现，而是发起一轮大规模的问题发现。

并行参与者包括：
- 多个 agent review design
- 人（贵平）自己 review
- 同事在飞书文档中评论
- agent 带着初稿，有目的地探索其他服务代码找问题

这一阶段的本质不是“润色文档”，而是：
- 找缺口
- 找风险
- 找遗漏
- 找与已有代码/服务的不一致之处

### Step 3：agent 整合评论 → 与人逐条讨论 → 更新 design
当评论和问题收上来之后：
- 先让 agent 整合这批评论
- 再逐条与人讨论：这些问题怎么解
- 解决完一批，就更新一次 design
- 拿着新的 design，再继续进入下一轮问题发现和讨论

所以这里不是一次性 review，而是一个显式的迭代回环：

> design → 多路找问题 → 整合评论 → 逐条讨论 → 更新 design → 再循环

这也是后续网页中应重点表达的核心回环之一。

### Step 4：技术评审插入 design 收敛阶段，并继续暴露新问题
在 design 收敛的过程中，还夹杂着一轮真正的**技术评审**。

这说明前期并不只是“写设计 + 收评论”，还会进入更现实的技术可行性检查。

技术评审中会继续提出新的问题，例如：
- 对现有存储的流量评估

这类问题已经不是表面措辞或结构调整，而是会影响方案判断的现实约束与技术风险。

### Step 5：带着技术评审提出的新问题，再和 Claude 一起找答案
当技术评审暴露出新问题之后，不是直接停住，而是：
- 带着这些新问题继续与 Claude 一起寻找答案
- 把问题变成进一步探索的输入
- 再将得到的答案收进 design

因此，design 的收敛不是简单编辑文档，而是一个持续吸收新问题、继续查证、继续修正的过程。

### Step 6：最终产出一版思路非常清晰的最终版 design
经过上述多轮 brainstorm、review、评论、技术评审和带问题回查之后，最终会产出一版**最终版 design**。

这份最终版 design 的特点是：
- 不是代码很多
- 但思路非常清晰
- 已经吸收了关键问题与约束
- 可以作为后续 plan / implementation 的稳定起点

### Step 7：跨仓任务拆解 / agent 拆分
当最终版 design 已经跨多个代码仓库时，下一步是让 Claude Code 帮助拆解 agent，实质上就是拆解任务。

拆分维度：
- **两个 for 仓库**：按仓库维度拆，每个仓库各自对应一个 agent
- **一个 for 规则引擎配置**：按专项能力维度拆，拉出横切的规则引擎配置作为独立 agent

这一步的本质：
- 把跨仓设计，变成可并行执行的多 agent 任务图
- 不是按单一维度拆，而是按仓库 + 按能力混合拆

### Step 8：每个 agent 内部跑一套 plan → review 回环
拆完 agent 之后，不是直接执行。

每个 agent 内部各自跑一套小回环：

1. **Claude Code 出 plan**
2. **Codex review plan**（异构 agent review）
3. **Planner（Claude Code）说哪些它不接受**
4. **人与 planner 讨论这些不接受的部分**
5. **修正 plan**
6. **再跑一轮回环...**

这个回环会跑多轮，直到 planner 和人都对这个 plan 满意。

关键点：
- 不是 Codex 直接决定接受或拒绝
- 不是人直接拍板
- 而是 planner 主动提出“我不接受”，再由人和 planner 一起讨论
- 讨论完修正 plan，再进入下一轮 review

这种回环模式使得每个 agent 的 plan 都经过充分的异构 review 和人机讨论后才进入实现。

### Step 9：是否过关、是否进入下一步，由 reviewer 判断
在这套 agent 内部回环里，reviewer 不只是提建议，而是承担了阶段出口的判断角色。

也就是说：
- planner 负责出 plan、吸收讨论、持续修正
- reviewer 负责判断 plan 是否已经足够清晰、合理、可以过关
- 人负责参与关键分歧讨论与取舍

因此，是否收敛、是否可以继续往下走，不由 planner 单独决定，而是**由 reviewer 判断**。

这使得 reviewer 在整个流程中不仅是“审稿人”，更像是每个 agent 回环里的质量闸门。

### Step 10：reviewer 放行后，agent 直接进入实现
当 reviewer 判断 plan 已经过关之后，这个 agent 不需要再回到统一总控层做二次批准，而是**直接进入实现**。

这意味着中间的治理方式是：
- 人参与 plan 回环中的关键分歧讨论
- reviewer 承担是否放行的出口判断
- 一旦放行，agent 直接进入 coding 阶段

### Step 11：进入实现后，又形成第二组回环
进入实现并不是线性往前，而是会再形成一组新的回环。

这里会：
- 启动一个新的 **Codex session** 来写代码
- 这个新 session 专门承担实现工作

写完之后，会拉起一组更强的并行 review：
- 两个 Claude Code session
- 一个 Codex session
- 两个 Mira session

也就是说，代码完成后会有 **5 路 reviewer** 同时检查。

### Step 12：写代码的 Codex session 汇总 review，并通过反驳机制修正代码
这一轮 review 的处理方式不是人手工逐条归并，而是：
- 由写代码的那个 Codex session 负责汇总各路 review
- 面对 review 提出的意见，不是来一条改一条，而是继续采用“反驳机制”
- coder session 可以吸收意见，也可以对部分意见进行反驳
- 最终据此继续修正代码，并视情况再进入下一轮 review

因此，这一层的实现回环可以概括为：

> 新 Codex session 写代码 → 多 reviewer 并行审查 → coder 汇总 → 反驳 / 吸收 → 修正代码 → 再循环

### Step 13：单 agent 局部收敛之后，进入跨 agent 的整体 reviewer 回环
单个 agent 内部实现回环收敛之后，并不会直接结束，而是会回到一个**更大的 reviewer**。

这个 reviewer 不再只看单个 agent，而是：
- **整体看三个 agent**
- 站在全局视角检查结果能否拼起来
- 判断有没有跨 agent 的问题
- 检查局部都对、整体是否仍然有漏洞

因此，这一层代表的是一个更高层级的整体回环，而不是普通 code review。

### Step 14：大的 reviewer 继续回环几轮，主要从整体视角检测
这个大的 reviewer 不是看一遍就结束，而是还会继续跑几轮回环。

这一轮关注的是：
- 系统级问题
- 跨 agent 边界问题
- 整体协同问题
- 局部正确但全局不成立的问题

它补上了一个关键现实：

> 局部都收敛，不代表整体收敛。

### Step 15：整体 reviewer 通过后，进入联调 / 测试 / 实跑验证
当大 reviewer 从整体视角放行之后，流程不会停在 review，而是进入：
- 联调
- 测试
- 实跑验证

也就是说，这套体系后面还有明确的真实验证闭环，而不是停在文档和 review 层。

### Step 16：验证阶段发现问题时，按问题性质回流
在联调 / 测试 / 实跑验证阶段，如果发现问题，回流路径不是固定的一条，而是**按问题性质回流**。

可能的回流方向包括：
- 回到对应实现 agent
- 回到大的 reviewer
- 回到 planner / plan 层
- 甚至在必要时回到更早的 design 层

因此，整张图的回流结构不是固定单回路，而是一个带条件分流的多层回流系统。

## 文字版 wireframe v1

### 页面总结构
- 顶部：标题 / 副标题 / 简短说明
- 中部：纵向 6 层主图
- 底部：图例 / 执行体说明 / 连线说明 / human manual execution 注释

### Layer 1 — Demand / Intent
- 一句话需求（起点）

### Layer 2 — Design Convergence Loop
- Brainstorm 初版 Design
- 多路问题发现（agent review / human review / 同事飞书评论 / 带 design 探索其他服务代码）
- 评论整合与逐条讨论
- 技术评审
- 带问题继续找答案
- 最终版 Design

这一层的重点是第一组大回环：
- design → 多路找问题 → 逐条讨论 → 技术评审 / 新问题 → 带问题继续找答案 → 更新 design → 再循环

### Layer 3 — Agent / Task Split
- 跨仓任务拆解
- Repo Agent A
- Repo Agent B
- Rule Engine Config Agent
- Human Manual Task（例如 web 操作、登录/点击/确认等 agent 不适合直接完成的任务）

### Layer 4 — Agent Plan Loops
每个 agent 内部都存在一套 plan 回环：
- Claude Code Plan
- Codex Review Plan
- Planner 不接受点
- Human ↔ Planner Discussion
- Plan Revision
- Reviewer Gate

这一层的重点是：
- reviewer 不是提建议而已，而是阶段出口判断者
- 人不是纯旁观者，而是参与关键分歧讨论

### Layer 5 — Implementation Loop + Global Reviewer Loop
#### 单 agent 实现回环
- New Codex Coding Session
- Parallel Review Cluster
  - Claude Code Review 1
  - Claude Code Review 2
  - Codex Review
  - Mira Review 1
  - Mira Review 2
- Coder Summarizes Reviews
- Rebut / Accept / Modify
- Code Revision

#### 跨 agent 的整体 reviewer 回环
- Global Reviewer
- Cross-Agent Issue Detection
- Global Revision Loop

这一层的重点是：
- 局部收敛不代表整体收敛
- 代码实现后仍需系统级整体 reviewer 从跨 agent 视角继续回环

### Layer 6 — Verification / Conditional Return
- Integration
- Testing
- Real Run Validation
- Conditional Return Router

这一层的重点是：
- 验证失败后的回流路径不是固定的
- 会按问题性质回到不同层级：实现 agent / 大 reviewer / planner / 甚至更早 design 层

### Human 的双重角色
#### 1. Controller
- 定目标
- 参与设计讨论
- 参与 planner 分歧讨论
- 参与整体判断

#### 2. Manual Executor
- 承接部分不适合 agent 的任务
- 重要人工任务单独成节点
- 次要人工介入用 badge / 小标记表示

### 节点规则
#### Agent session 节点
显示：
- session 名称
- 执行体
- 产出物

#### Human manual task 节点
- 用于承接重要人工操作

#### Human intervention 标记
- 用于表示某些节点需要人工介入，但不足以单独成节点

### 连线规则
至少区分：
- 主推进线
- 并行展开线
- 回环线
- 汇总线
- 条件回流线

### 执行体视觉编码建议
- Claude Code：蓝色系
- Codex：绿色系
- Mira：紫色系
- Human：中性偏金/灰
- Reviewer Gate / Global Reviewer：高对比强调色

### 当前状态
目前已完成 brainstorm + 文字版 wireframe v1，**还没有进入网页实现**。
后续如果继续推进，下一步应补：
- 更接近最终页面的 wireframe v2
- 标题 / 副标题候选
- 是否采用单文件静态 HTML 原型或接入现有项目
- 正式 spec / plan / implementation
