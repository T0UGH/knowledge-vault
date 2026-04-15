# Hermes Profile Handbook

- 日期：2026-04-15
- 目标：给未来的自己 / Codex 一份**可以直接照做**的 Hermes profile 配置手册
- 适用场景：想把 Hermes 分成多个用途明确、配置隔离的 agent 实例，例如 coding / research / ops

---

## 1. 先说建议：我推荐怎么拆 profile

如果没有很特殊的需求，先不要一口气拆太多。

### 推荐最小方案：2 个 profile

1. `default`
   - 继续保留现在默认的 Hermes
   - 用于日常聊天 / 通用任务 / 不想折腾时的主入口

2. `coder`
   - 专门给代码 / 仓库 / 自动化 / tmux + Codex 协作使用
   - 以后如果要给 coding agent 更激进的 prompt / skills / memory，也都放这里

### 推荐实用方案：3 个 profile

1. `default`
   - 通用主入口
2. `coder`
   - 代码 / repo / tmux / Codex / push
3. `research`
   - 调研 / 资料整理 / 长文档 / 监控 / 内容策展

### 如果你以后真要再拆，可以再加

4. `ops`
   - cron、发布、监控、消息路由、定时任务偏多时再拆

> 结论：**现在最值得先建的是 `coder`。**

---

## 2. Hermes profile 到底是什么

每个 profile 都是一个**独立的 HERMES_HOME**。

大致会有自己独立的：
- `config.yaml`
- `.env`
- `SOUL.md`
- `memories/`
- `sessions/`
- `skills/`
- `logs/`
- `cron/`
- `workspace/`
- `home/`（给 subprocess 隔离 git / ssh / gh / npm 等配置）

默认 profile：
- `~/.hermes`

新 profile 默认在：
- `~/.hermes/profiles/<name>/`

所以 profile 不是“换个名字”，而是**实际隔离运行环境**。

---

## 3. 最推荐的创建方式

### 方案 A：基于当前配置克隆一个 `coder`

如果你现在默认 Hermes 里已经有：
- 可用的模型配置
- API keys
- 比较成熟的 SOUL / MEMORY / USER

那最省事的方法是：

```bash
hermes profile create coder --clone
```

它会带过去：
- `config.yaml`
- `.env`
- `SOUL.md`
- `memories/MEMORY.md`
- `memories/USER.md`

这个方案最适合你现在的阶段。

### 方案 B：建一个完全干净的 `coder`

如果你就是想彻底从头配：

```bash
hermes profile create coder
```

然后自己重新 `setup`。

### 我的建议

**先用 `--clone`。**

原因：
- 不会丢掉已经调好的基础人格和 key
- 但又不会把全部运行态垃圾一股脑复制进去
- 最适合“我要一个 coding 专用分身”这种需求

---

## 4. 推荐的完整执行步骤（给 Codex 照做）

下面这套流程是最实用的。

### Step 1：创建 `coder` profile

```bash
hermes profile create coder --clone
```

### Step 2：切成默认 profile（可选）

如果你希望以后直接运行 `hermes` 就默认进 `coder`：

```bash
hermes profile use coder
```

如果你不想改默认，也可以跳过，以后都用：

```bash
hermes -p coder chat
```

### Step 3：检查 profile 是否创建成功

```bash
hermes profile list
hermes profile show coder
```

你应该能看到：
- profile 路径
- model
- gateway 状态
- `.env` 是否存在
- `SOUL.md` 是否存在
- alias 是否创建成功

### Step 4：检查 wrapper alias 是否可用

理想情况：

```bash
coder chat
```

如果报 `command not found`，大概率是 `~/.local/bin` 不在 PATH。

补到 shell 配置里：

```bash
export PATH="$HOME/.local/bin:$PATH"
```

然后重新开一个 shell 再试。

### Step 5：按 coding 用途微调 `coder`

重点建议改这几类东西：

1. `SOUL.md`
   - 明确这个 profile 偏 coding / repo / automation
   - 对“完成”的定义更严格：修改 → 测试 → push → 报 hash

2. `memories/MEMORY.md`
   - 放 coding / repo / tool 相关约束
   - 少放泛聊天内容

3. `config.yaml`
   - 如果要单独切模型、工具开关、显示配置，可以在这里调

4. `.env`
   - 如果以后想让 coding profile 用不同 key、不同 provider，也在这里拆

---

## 5. 我建议 `coder` profile 重点调整什么

Codex 配的时候，不是“只是复制一个 profile 就完了”，建议重点做下面这些微调。

### A. 定义清楚 `coder` 的职责

推荐目标：
- repo 分析
- coding 设计 / 实施配合
- tmux + Codex 协作
- 测试、验证、提交、push
- 少做泛研究型聊天

### B. 把 coding 的完成标准写死

建议明确成：
- 修改代码
- 跑相关测试 / smoke
- 确认结果
- 提交
- push
- 回报 commit hash

### C. 区分“代码任务”和“文档任务”

一个很实用的约束：
- 代码文件改动 → 优先 Codex
- 文本 / spec / prompt / Markdown 说明 → Hermes 自己可直接处理

### D. 保留独立 memory / skill 空间

因为 `coder` 本来就该形成一套更偏工程的长期记忆：
- 哪些仓库怎么跑
- 哪些测试要怎么补依赖
- 哪些提交流程是必须的

---

## 6. 什么时候不要新建 profile

不是所有事情都值得拆 profile。

以下情况暂时不值得：
- 只是想试一个 prompt
- 只是临时切个模型
- 只是做一天的小实验

这类更适合直接在现有 profile 里试。

值得拆 profile 的情况：
- 你希望**长期**保持一种不同工作模式
- 你希望 memory / config / skills 和默认 agent 分开
- 你希望 gateway / cron / identity 也跟默认隔离

---

## 7. 常用 profile 命令速查

### 查看当前 active profile
```bash
hermes profile
```

### 列出所有 profile
```bash
hermes profile list
```

### 查看某个 profile 详情
```bash
hermes profile show coder
```

### 创建新 profile
```bash
hermes profile create coder
hermes profile create coder --clone
hermes profile create coder --clone-all
```

### 使用某个 profile 作为默认
```bash
hermes profile use coder
```

### 切回默认 profile
```bash
hermes profile use default
```

### 直接用 profile 启动聊天
```bash
hermes -p coder chat
```

### 删除 profile
```bash
hermes profile delete coder
```

---

## 8. 给 Codex 的执行 brief（可直接复制）

如果回家想让 Codex 帮你配，可以直接给它这段：

```text
请帮我为 Hermes 建一个新的 profile，名字叫 coder。

目标：
- 基于当前默认 profile 克隆关键配置，不要完全从零开始
- profile 主要用于 coding / repo / automation / tmux + Codex 协作
- 创建后检查它是否能正常使用
- 如果 wrapper alias 可用，确认 `coder chat` 可以工作；如果不行，检查 `~/.local/bin` 是否在 PATH
- 给出你实际改了哪些文件，以及 coder profile 的目录位置

具体要求：
1. 执行 `hermes profile create coder --clone`
2. 执行 `hermes profile show coder` 和 `hermes profile list`
3. 如果需要，把 `~/.local/bin` 加进 shell PATH
4. 检查 `~/.hermes/profiles/coder/` 下是否已经有：
   - config.yaml
   - .env
   - SOUL.md
   - memories/MEMORY.md
   - memories/USER.md
5. 视情况微调 `coder` 的 SOUL / memory，让它更偏 coding agent
6. 不要破坏 default profile
7. 最后把实际结果和建议的下一步告诉我
```

---

## 9. 我自己的推荐结论

如果现在只做一件事：

### 就做这个
```bash
hermes profile create coder --clone
hermes profile show coder
```

然后再决定要不要：
```bash
hermes profile use coder
```

### 为什么
因为这条路径：
- 最省事
- 最稳
- 最适合“先把 coding profile 跑起来，再慢慢打磨”

---

## 10. 后续还可以继续研究的点

后面如果要继续完善，可以再补这些专题：

1. 多 profile 的 gateway / service 并存方式
2. 不同 profile 的 `.env` / 模型 / provider 隔离策略
3. Honcho / memory 插件在 profile 下怎么工作
4. 如何导出 / 迁移一个 profile

这篇先解决最实际的问题：
**怎么新建一个 Hermes profile，并让 Codex 可以照着配。**
