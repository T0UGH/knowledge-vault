---
title: OpenClaw 工作台开荒指南
date: 2026-04-06
tags:
  - openclaw
  - bootstrap
  - workstation
  - guide
  - cli
status: active
aliases:
  - OpenClaw 开荒指南
  - OpenClaw 工作台指南
---

# OpenClaw 工作台开荒指南

这份文档不是教你“怎么把 OpenClaw 安装成功”，而是教你：

> **OpenClaw 装好之后，怎么把它变成一个真正能用的个人工作台。**

重点是：
- 默认工具路由
- 本机长期协作模式
- Obsidian 知识库
- 自建 skill 工作流
- 常用 CLI 工具箱

不展开 mailbox；那是后续增强，不是开荒第一天要解决的问题。

---

# Part 1：先建立心智模型

## 1. OpenClaw 不只是聊天入口，而是长期运行的个人工作台

很多人第一次装完 OpenClaw，会下意识把它当成：

- 一个能聊天的 agent
- 一个能调工具的壳
- 一个比 Claude Code 更常驻一点的助手

这理解不算错，但太浅。

更准确地说，OpenClaw 在个人机器上的价值是：

> **把 agent、工具、记忆、知识库、平台账号能力和长期协作习惯收成一个持续运行的工作台。**

它不是一次性任务执行器，而更像一个长期搭档 runtime。

所以开荒时最重要的不是“再装几个模型”，而是先把这几件事想清楚：

1. 你的长期知识放哪里
2. 你的默认工具路由是什么
3. 你的运行态 agent 和执行型 agent 怎么分工
4. 你的自建 skill 真身放哪里
5. 什么任务该在聊天里做，什么任务该让 CLI / agent 去做

---

## 2. 先接受一个现实：你真正需要的是“默认路由”

如果每次遇到任务都重新想：

- 用浏览器还是 CLI？
- 用 Claude Code 还是 OpenClaw？
- 用哪个 skill？
- 内容存哪里？

那这套系统很快会变成认知负担。

所以开荒时必须先建立**默认路由**。

### 建议的默认路由

- **普通网页 / 博客 / 文档页** → `defuddle`
- **登录态平台站点 / 站内搜索 / 简单交互** → `opencli`
- **跨平台搜索 / 信息收集 / 全网情报** → `agentreach`
- **飞书结构化操作** → `feishu-cli` / Feishu skills
- **GitHub 操作** → `gh`
- **写作 / 改写 / 分发** → `baoyu-*` 技能组
- **知识库治理** → `knowledge-vault-governance`
- **大型编码任务** → Claude Code / `tmux`
- **长期知识沉淀** → Obsidian `knowledge-vault`

这一步非常关键。

> **不要每次临场重选，先把默认路径固定下来。**

---

## 3. 这台机器上建议的协作分工

一个比较顺手的分工模型是：

### MT
主控、判断、规划、收口、知识沉淀。

适合做：
- 问题定义
- 工具路由判断
- 方案设计
- 目录治理
- 文档沉淀
- 阶段收口

### HappyClaw
长期运行宿主。

它不是一个“另一个人格”，而是让 agent 真正长期待在这台机器上的运行环境。

### Hex
跑在 HappyClaw 里的长期搭档 agent。

适合做：
- 执行
- 草稿实现
- 基础验证
- 低成本整理

不适合承担最终架构判断。

### Claude Code
偏 coding / review / implementation 的工程执行器。

适合做：
- 代码实现
- PR 修复
- 大仓库改动
- 较重的工程探索

在这台机器上，如果要启动 Claude Code，默认稳定路径是：

> **`tmux`，不是 ACP。**

---

## 4. Obsidian 不是记事本，是长期知识层

如果你想让 OpenClaw 真正变成工作台，就要有一个稳定的长期知识库。

建议做法：
- 用 Obsidian 承载长期知识
- 用一个独立 git 仓库管理，例如 `knowledge-vault`
- 把项目笔记、研究判断、概念、参考资料分层组织

### 推荐分层

```text
00-Home
01-Inbox
02-Projects
03-Research
04-Concepts
05-TODO
06-Reference
07-Posts
```

### 关键原则
- `Projects`：活项目 / 推进中的工作区
- `Research`：理解、比较、架构判断、源码阅读
- `Concepts`：跨项目的方法论与抽象概念
- `Reference`：稳定事实、文档镜像、手册
- `Posts`：成稿或内容产出

### 一个真实经验
当笔记量变大以后，真正的治理重点不再只是“放对目录”，而是：

> **高密度目录必须有 README / index。**

否则你还是只能靠搜索，不是在用结构化知识库。

---

## 5. 自建 skill 要把“真身”和“安装态”分开

这是开荒时很容易忽略，但后面非常值钱的一步。

不要把 skill 长期散落在：
- `~/.agents/skills/`
- `~/.openclaw/workspace/skills/`
- 各种临时目录

更好的做法是：

### 推荐结构
- **真身仓库**：`~/workspace/agent-skills`
- **运行态安装**：通过 `npx skills` 安装到本地 agent 目录

### 原则
> **source repo = 真身；agent 运行目录 = 安装态。**

这样后面你才能：
- 版本管理
- 跨机器迁移
- 分享给别人
- 逐步积累一套自己的 skill 库

---

# Part 2：默认工具箱

## 1. 信息获取

### `agentreach`
适合：
- 搜全网信息
- 查 Twitter / Reddit / YouTube / GitHub / 文章
- 做轻量研究

它是“广覆盖信息入口”。

### `defuddle`
适合：
- 普通网页
- 教程
- 博客
- 文档页

作用：
- 把网页抽成干净 Markdown
- 降低阅读噪音和 token 消耗

默认建议：
> **普通网页先过 `defuddle`。**

### `opencli`
适合：
- 站内搜索
- 登录态网站
- 平台级读取/简单操作

比如：
- X / Twitter
- YouTube
- B 站
- 小红书
- 微博
- Reddit
- Hacker News

它比瞎上浏览器自动化更稳定。

---

## 2. 飞书工具链

### `feishu-cli` / Feishu skills
适合：
- 文档
- 云盘
- 权限
- wiki

默认建议：
> **飞书结构化操作优先 CLI / skill，不优先浏览器。**

---

## 3. Git / GitHub

### `gh`
适合：
- 建 repo
- 查 issue / PR
- 看 CI
- 发 release

如果你本来就深度用 GitHub，`gh` 是必装。

默认建议：
- repo 管理、PR、Actions 先想 `gh`
- 不是每次都回网页点

---

## 4. 写作 / 分发

### `baoyu-*` 技能组
这里非常适合内容创作者。

建议优先掌握这些：
- `baoyu-format-markdown`
- `baoyu-translate`
- `baoyu-markdown-to-html`
- `baoyu-post-to-wechat`
- `baoyu-post-to-x`
- `baoyu-post-to-weibo`
- `baoyu-cover-image`
- `baoyu-image-gen`

### `humanizer`
适合：
- 去 AI 味
- 收口成稿语言
- 让文章更自然

---

## 5. Obsidian / 知识沉淀

### `obsidian-cli`
和正在运行的 Obsidian 交互。

### `obsidian-markdown`
帮助写符合 Obsidian 习惯的 Markdown。

### `knowledge-vault-governance`
治理知识库结构。

适合：
- 看哪里乱
- 生成整理方案
- 你确认后执行迁移
- 最后 git 收尾

---

## 6. 编码 / 工程执行

### Claude Code
适合：
- 大型编码任务
- PR 修复
- 工程实现
- 重构

### `tmux`
在这台机器上，Claude Code 的可靠启动路径是：

> **`tmux` + 启动脚本 / 环境变量**

不要默认走 ACP。

---

# Part 3：开荒 Checklist

## 1. 装好 OpenClaw 后，先做这 5 件事

1. **确认 OpenClaw 能正常跑**
2. **准备一个长期知识库（Obsidian + git）**
3. **确认默认工具路由**
4. **建立自建 skill 真身仓库**
5. **明确长期协作 agent 分工**

---

## 2. 准备长期知识库

建议：
- 建一个独立仓库，例如 `knowledge-vault`
- 用 Obsidian 打开它
- 初始目录至少有：

```text
00-Home
02-Projects
03-Research
06-Reference
```

然后再慢慢扩充。

### 最重要的不是目录多，而是：
- 目录语义要稳定
- 高密度目录要有 README/index
- 不要让 root 留孤儿文件

---

## 3. 建自建 skill 仓库

建议：
- 仓库位置：`~/workspace/agent-skills`
- 单仓多 skill
- 以后所有自建 skill 的真身都放这里

### 安装本地 skill
在仓库根目录执行：

```bash
npx skills add . --skill <name> --agent '*' --copy -y
```

例如：

```bash
cd ~/workspace/agent-skills
npx skills add . --skill knowledge-vault-governance --agent '*' --copy -y
```

---

## 4. 先建立几个默认习惯

### 习惯 1：普通网页先走 `defuddle`
不要直接硬读原网页。

### 习惯 2：GitHub 操作先想 `gh`
不要默认回网页点。

### 习惯 3：飞书结构化操作先走 `feishu-cli`
不要优先浏览器点点点。

### 习惯 4：内容和长期知识及时沉淀进 Obsidian
不要把高价值判断只留在聊天记录里。

### 习惯 5：自建 skill 不要直接写在运行目录
先写 repo，再安装。

---

## 5. 最小排障入口

如果东西“不工作了”，先看是哪一层坏：

### OpenClaw 层
看：
- `openclaw status`
- gateway 是否正常

### 工具层
看：
- CLI 本身是否能跑
- 登录态是否在
- 命令是否走对路由

### 知识库层
看：
- 文件是否落到了正确目录
- 是否缺 README/index
- 是否出现重复真身

### Agent 层
看：
- 是主控问题
- 还是 HappyClaw / Hex 执行问题
- 还是 Claude Code 工程执行问题

---

# Part 4：一套能跑起来的默认工作流

给新机器最实用的不是“全学会”，而是先形成一条最小闭环：

1. **OpenClaw 负责长期主控**
2. **Obsidian 负责长期知识沉淀**
3. **`agentreach` / `opencli` / `feishu-cli` 负责外部信息和平台操作**
4. **Claude Code 负责重工程执行**
5. **自建 skill 放进 `agent-skills` 仓库管理**

一句话：

> **OpenClaw 不是孤立装一个 agent，而是把 agent、CLI、知识库、内容工作流和个人协作习惯编成一个系统。**

---

## 最后建议

如果你的朋友刚装好 OpenClaw，不要让他一上来就追求：
- 多模型
- 多 agent
- 多平台
- 自动化拉满

第一阶段先把这 4 件事跑顺：

1. 一个长期主控 agent
2. 一个稳定知识库
3. 一套默认工具路由
4. 一个自建 skill 仓库

这 4 个跑顺了，后面才是真正的扩展，而不是堆功能。
