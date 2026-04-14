---
title: architecture-diagram-generator 项目观察
date: 2026-04-15
tags:
  - architecture-diagram
  - claude-code
  - skill
  - project-research
  - research
status: done
source_files:
  - /Users/wangguiping/workspace/architecture-diagram-generator/README.md
  - /Users/wangguiping/workspace/architecture-diagram-generator/architecture-diagram/SKILL.md
  - /Users/wangguiping/workspace/architecture-diagram-generator/architecture-diagram/assets/template.html
related_notes:
  - 03-Research/Claude Code/2026-04-last30days-skill-项目探索调研报告.md
---

# architecture-diagram-generator 项目观察

## 结论

先给判断：

> **`Cocoon-AI/architecture-diagram-generator` 不是一个真正的“架构图生成引擎”，而是一个把 Claude 约束成稳定出图器的 skill 包。**
>
> **它的核心价值不在自动建模，而在“用 prompt 规则 + 视觉模板，让模型快速产出一张可交付的漂亮架构图”。**

如果再压缩一句：

> **这是一个高性价比的 AI 出图壳，不是长期架构资产管理系统。**

## 它到底是什么

从仓库结构看，这个项目非常轻：

- `README.md`：安装、使用方式、示例提示词
- `architecture-diagram/SKILL.md`：告诉 Claude 该怎么理解任务、怎么画图
- `architecture-diagram/assets/template.html`：固定的页面壳、样式、SVG 模板
- `examples/*.html`：几份出图样例

它的工作流也很简单：

1. 先准备一段系统架构描述文本
2. 再让 Claude 调用这个 skill
3. Claude 按 `SKILL.md` 的规则，把内容填进 `template.html`
4. 最终产出一个单文件 HTML，其中嵌了 CSS 和 SVG

所以它本质上不是：

- DSL 编译器
- 自动布局引擎
- draw.io / Excalidraw / Mermaid 替代品
- 可维护的架构建模系统

更准确的说法是：

> **一个以 Claude 为 runtime 的“架构图模板化生成器”。**

## 为什么它能看起来很有效

这类项目能打，不是因为“技术栈复杂”，而是因为它压中了 AI 最擅长的工作方式：

- 输入是自然语言描述
- 输出是一个固定结构的 HTML 文件
- 中间用强约束模板把模型乱跑的空间压小

`SKILL.md` 里写得很细，已经不是一句“画个图”那么松，而是把视觉系统直接写死：

- 组件类型对应固定颜色
- 背景、字体、边框、圆角、虚线边界全部定好
- 箭头的 z-order、遮挡方式、图例位置、间距规则都给出明确约束
- 输出格式固定为单个 HTML 文件，不依赖 JS

这其实是它最聪明的地方：

> **它没有去解决“通用图形排版”这个难题，而是把问题降维成“让模型在一个很强的设计系统里补内容”。**

## 它解决的真实问题

它解决的是：

> **当用户只想尽快拿到一张足够专业、可以分享、可以放文档里的架构图时，怎么让 AI 直接给出一个像样结果。**

这类场景包括：

- 给文章配一张系统图
- 给需求评审补一张架构草图
- 给同事讲某个服务关系时，先快速出第一版
- 在方案讨论阶段，用图帮助对齐，而不是先追求标准化建模

在这些场景下，它的性价比很高，因为：

- 不需要额外作图软件
- 不需要设计能力
- 出图速度快
- 结果天然可分享

## 它不解决什么

它的边界也非常明确。

### 1. 不是精确布局系统

复杂图一多，模型仍然可能出现：

- 组件重叠
- 连线绕不过去
- 图例侵入主体区域
- 层次关系表达不稳定

`SKILL.md` 里之所以要把间距、图例位置、遮挡规则写得很死，恰恰说明作者在对抗这些问题。

### 2. 不是长期维护的架构资产格式

它输出的是 HTML + SVG，适合看、适合分享，但不天然适合：

- 做结构化 diff
- 做大规模图谱维护
- 作为企业架构资产源文件长期演进
- 跟配置或基础设施代码做强绑定生成

也就是说，它更偏“交付图”，不是“源模型”。

### 3. 不是建模工具链

它不像 Mermaid / PlantUML 那样有明确 DSL，也不像 draw.io / Excalidraw 那样有人机协作编辑体验。

所以它更适合：

- 快速产出第一版
- 生成可嵌入文档的视觉稿
- 让 Claude 在 chat 中迭代几轮

不太适合：

- 追求严格规范的长期体系化建模
- 高复杂度、多人长期维护的大型图仓

## 工程判断：值不值得用

我的判断是：**值得装，但不要高估。**

推荐把它当成：

> **“架构图快速出稿器”**

不推荐把它当成：

> **“架构图基础设施”**

### 值得用的原因

1. **成本极低**：skill + 模板就能工作
2. **效果很讨巧**：生成的图卖相比 Mermaid 更强
3. **适合 AI 协作**：你本来就习惯让模型基于描述出初稿
4. **输出独立**：一个 HTML 文件就能交付

### 不该高估的地方

1. 复杂架构图的稳定性有限
2. 维护能力弱于 DSL
3. 依赖模型遵守模板规则，不是硬约束系统
4. 很难成为标准化团队图资产的单一来源

## 和前面几个 skill 项目的共同点

这个项目和 `andrej-karpathy-skills`、`last30days-skill` 有一个明显共性：

> **都不是靠重 runtime 取胜，而是把高价值经验压缩成 prompt policy / skill / template。**

具体到这个项目，它压缩的是：

- 视觉规范
- 架构图常见元素
- HTML/SVG 出图模板
- 避免图形错乱的经验规则

这条路线很适合当前 AI 工具生态：

- 成本小
- 分发快
- 复用强
- 对模型友好

## 推荐接入姿势

如果要接入到自己的工具体系，建议按这个定位使用：

### 适合的用法

- 文章配图
- 方案讨论图
- 项目 README / 设计说明中的首版系统图
- 从代码仓分析结果生成一版“讲解型架构图”

### 更合理的协作流

1. 先让 agent 分析系统，产出结构化架构描述
2. 再调用该 skill 生成首版 HTML 图
3. 人工 review 关键关系是否正确
4. 如果图要长期维护，再决定是否转成 Mermaid / PlantUML / Excalidraw 等更适合沉淀的格式

这比一上来就指望它承担全部建模职责更稳。

## 最终判断

最后收一句：

> **`architecture-diagram-generator` 的真正价值，不是“把画图问题彻底自动化”，而是把“从架构描述到一张能交付的图”这段路径压得足够短。**

所以它是一个很好的“前端出图壳”，但不是最终的架构建模终局。

对于当前以 AI 协作为主的工作流，这种东西值得留着，尤其适合快速对齐、写文档、做说明图。
