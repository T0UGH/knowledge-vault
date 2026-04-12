---
title: Hermes 图表 skill 安装与用途对照
tags:
  - hermes
  - skills
  - diagramming
  - mermaid
  - uml
  - vega
---

# Hermes 图表 skill 安装与用途对照

仓库：<https://github.com/markdown-viewer/skills>

## 结论

这批从 `markdown-viewer/skills` repo 装进来的 15 个 skill，本质上是 **给 Hermes 补齐常见图表/可视化表达能力**。

适合的默认选型：
- 系统设计默认首选：`mermaid`
- 正式软件工程表达：`uml`
- 企业级全景架构：`archimate`
- 云上部署架构：`cloud`
- 业务流程：`bpmn`
- 数据链路 / 数仓：`data-analytics`
- 网络 / 安全专项：`network` / `security`
- 偏展示传播：`infocard` / `infographic`
- 需要自由摆放：`canvas`
- 需要做数据图表：`vega`

## 已安装并验证可见

已安装列表：
- `archimate`
- `architecture`
- `bpmn`
- `canvas`
- `cloud`
- `data-analytics`
- `graphviz`
- `infocard`
- `infographic`
- `iot`
- `mermaid`
- `network`
- `security`
- `uml`
- `vega`

验证证据：
- 本地 `~/.hermes/skills/<name>/SKILL.md` 全部存在
- 实测 `skill_view()` 可直接读取：
  - `mermaid` ✅
  - `uml` ✅
  - `vega` ✅
- 说明不是“文件在磁盘上但 Hermes 看不见”，而是 **已进入当前 Hermes 的可见 skill 目录**

## 能干什么：15 个 skill 对照表

| Skill | 适合画什么 | 典型用途 | 判断 |
|---|---|---|---|
| `archimate` | 企业架构图 | 业务 / 应用 / 技术层全景 | 适合做大盘视角 |
| `architecture` | 分层架构图 | 系统分层、模块边界、依赖关系 | 通用、工程味强 |
| `bpmn` | 业务流程图 | 审批流、运营流程、跨角色协作流 | 业务流程标准表达 |
| `canvas` | 自由布局图 | 头脑风暴、概念关系、非结构化草图 | 最灵活，不强约束 |
| `cloud` | 云架构图 | AWS / GCP / Azure 部署架构 | 适合云资源表达 |
| `data-analytics` | 数据分析 / 数据管道图 | ETL、指标链路、数据流转 | 面向数据体系 |
| `graphviz` | 通用关系图 | 依赖图、调用图、树状结构 | 自动布局强，适合复杂关系 |
| `infocard` | 信息卡片 | 单页摘要、知识卡片、结论卡 | 偏展示，不偏系统设计 |
| `infographic` | 信息图 | 汇报材料、讲解型内容、视觉化总结 | 偏传播与说明 |
| `iot` | 物联网架构图 | 设备、网关、云端、控制链路 | 适合边缘 / 设备场景 |
| `mermaid` | 通用工程图 | 流程图、时序图、状态图、ER 图 | 默认首选，写得快 |
| `network` | 网络拓扑图 | 子网、交换机、防火墙、链路 | 网络表达专业 |
| `security` | 安全架构图 | 认证、授权、边界防护、控制点 | 适合安全评审 |
| `uml` | UML 图 | 类图、时序图、用例图、活动图 | 软件工程标准表达 |
| `vega` | 数据图表 | 折线图、柱状图、散点图 | 面向数据展示，不是架构图 |

## 实用选型速查

| 你要表达的东西 | 优先用哪个 |
|---|---|
| 系统模块关系 | `architecture` / `mermaid` |
| 接口调用时序 | `uml` / `mermaid` |
| 业务流程 | `bpmn` |
| 云资源部署 | `cloud` |
| 企业架构全景 | `archimate` |
| 数据流 / ETL / 数仓 | `data-analytics` |
| 网络拓扑 | `network` |
| 安全边界 / 控制点 | `security` |
| 复杂依赖网 / 关系网 | `graphviz` |
| 文章配图 / 知识卡片 | `infocard` / `infographic` |
| 指标图表 | `vega` |
| 不想被固定语法束缚 | `canvas` |

## 怎么装

### 方案 A：标准安装方式（优先）

在 skills 仓库根目录执行：

```bash
npx skills add . --skill <name> --agent '*' --copy -y
```

如果是逐个安装这 15 个 skill，就把 `<name>` 换成具体 skill 名即可。

### 方案 B：一批图表 skill 的本地复制安装（这次实际能跑通的兜底思路）

这次遇到的真实坑是：
- `npx skills add ...` 过程中，底层走 Git clone 时多次被 GitHub SSL 抖动卡住
- 这类问题不是 skill 本身有问题，而是 **获取路径不稳定**

因此实际可行的兜底思路是：
1. 先把 `markdown-viewer/skills` repo 拿到本地
2. 确认目标 skill 的目录完整，包含 `SKILL.md`
3. 把目标 skill 安装到当前 Hermes 可见目录 `~/.hermes/skills/`
4. 再用 `skill_view(<name>)` 做可见性验证

一个最小可用流程可以写成：

```bash
# 先在本地拿到仓库
 git clone https://github.com/markdown-viewer/skills.git

# 以 mermaid 为例，把 skill 复制到 Hermes 可见目录
mkdir -p ~/.hermes/skills
cp -R skills/mermaid ~/.hermes/skills/

# 验证目录完整
test -f ~/.hermes/skills/mermaid/SKILL.md && echo ok

# 最终以运行时可见性为准
# 在 Hermes 里执行：skill_view("mermaid")
```

核心判断：

> 安装完成的标准，不是“磁盘上有文件”，而是 `skill_view()` 能直接读到。

## 这次的真实坑与填坑结论

### 坑 1：GitHub SSL 抖动会让 `npx skills add ...` 看起来像 skill 安装失败
不推荐把问题误判成：
- skill 包坏了
- Hermes 不支持
- skill 目录结构不对

更合理的判断是：
- 很可能只是下载链路抖动
- 可以切到本地 repo / 本地复制方式继续推进

### 坑 2：只看 `~/.hermes/skills` 目录不够
因为“文件存在”不等于“系统可见”。

最终验证动作应该是：
- 看目录：`~/.hermes/skills/<name>/SKILL.md`
- 再看运行时：`skill_view("<name>")`

### 坑 3：图表 skill 很容易装了但不会选
这批 skill 的主要价值不是“数量多”，而是 **把不同表达任务路由到对的图表语法**。

如果没有选型表，后面会反复在 `mermaid / uml / graphviz / vega` 之间瞎试。

## 当前状态

当前 Hermes 已能直接读取至少以下 skill：
- `mermaid`
- `uml`
- `vega`

因此可以判断：
- 这批图表 skill 已经接入成功
- 后面进入的是“怎么用得顺手”的问题，不是“系统看不见”的问题
