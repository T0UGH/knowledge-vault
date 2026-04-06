# Signal Engine

## 这个项目是干什么的

这是从 `daily-lane` 演进出来的新方向项目入口。

当前共识：
- 新项目名称：**Signal Engine**
- lane 后续定位为 **collect engine**
- 日报生成仍然交给 agent
- collect 层只做好 collect，不承担候选裁剪式决策
- 过滤 / 去重 / 打分 / 排序 属于概念独立的一层，但暂不急着开工
- 运行纪律层（diagnose / check-config / mock / status）是近期优先方向
- 后续准备从 shell-first runtime 迁向 **Python CLI**
- **Signal Engine 第一版职责只到 collect**：collect / normalize / signal / index / state / diagnose / status / config-check / mock；不做 filtering / ranking / synthesis
- 输出策略：**保留现有 Markdown/signal 产物，同时补一个很薄的 `run.json`**
- `run.json` 定位为 **run-level manifest / 运行收据**，不是第二套 signal 内容系统
- `run.json` 第一版规则：**每 lane 每日一份、覆盖写、成功失败都写**

## 当前讨论焦点

1. Python CLI 的项目定位与边界
2. 新 CLI runtime 的命令结构
3. 哪些 lane 先迁、哪些后迁
4. collect engine 与 future processing layer 的关系
5. 运行纪律层哪些先做、哪些后做

## 当前迁移难度判断（初稿）

从低到高：
1. `x-feed`
2. `x-following`
3. `github-trending-weekly`
4. `github-watch`
5. `product-hunt-watch`

## Python 视角下的参考骨架（当前建议）

### 总体原则

第一版先做成：
- **Python CLI**
- **collect-first**
- **command group 清楚**
- **shared/runtime-support 单独抽层**
- **兼容当前 signal/state/index 目录协议**
- 暂时不做 plugin / registry / 动态 adapter 系统

一句话：

> 先做一个干净、稳的 Python collect CLI，不先做 opencli 那种平台。

### 推荐目录结构

```text
signal-engine/
├─ README.md
├─ pyproject.toml
├─ src/
│  └─ signal_engine/
│     ├─ cli.py
│     ├─ main.py
│     ├─ commands/
│     │  ├─ collect.py
│     │  ├─ diagnose.py
│     │  ├─ status.py
│     │  ├─ config.py
│     │  └─ lanes.py
│     ├─ core/
│     │  ├─ config.py
│     │  ├─ context.py
│     │  ├─ errors.py
│     │  ├─ logging.py
│     │  ├─ paths.py
│     │  ├─ result.py
│     │  └─ types.py
│     ├─ runtime/
│     │  ├─ collector.py
│     │  ├─ diagnose.py
│     │  ├─ status.py
│     │  ├─ outputs.py
│     │  └─ state.py
│     ├─ lanes/
│     │  ├─ base.py
│     │  ├─ github_watch.py
│     │  ├─ github_trending_weekly.py
│     │  ├─ x_feed.py
│     │  ├─ x_following.py
│     │  └─ product_hunt_watch.py
│     ├─ sources/
│     │  ├─ github/
│     │  ├─ x/
│     │  └─ product_hunt/
│     ├─ signals/
│     │  ├─ writer.py
│     │  ├─ index.py
│     │  ├─ schema.py
│     │  └─ frontmatter.py
│     ├─ state/
│     │  ├─ store.py
│     │  ├─ diff.py
│     │  └─ keys.py
│     ├─ output/
│     │  ├─ text.py
│     │  ├─ json.py
│     │  └─ markdown.py
│     └─ utils/
│        ├─ dates.py
│        ├─ shell.py
│        ├─ files.py
│        └─ strings.py
├─ tests/
└─ docs/
```

### 各层职责

#### `commands/`
CLI 命令层，只做参数解析与分发，不堆业务逻辑。

#### `core/`
承载最底层公共骨架：config、paths、context、errors、通用结果对象。

#### `runtime/`
承载运行纪律层：collect orchestration、diagnose、status、outputs coordination、state access。

#### `lanes/`
一条 lane 一个 Python 模块。概念上统一暴露三类能力：
- `collect`
- `diagnose`
- `status`

#### `sources/`
底层取数与 probe 能力。lane 不直接到处拼 `gh api` 或 shell 命令，而是调用 source modules。

#### `signals/`
Signal Engine 的核心 domain 层，集中处理：
- signal frontmatter
- signal file naming
- signal writer
- index generator
- output schema

#### `state/`
显式承载 state 读写与 diff 逻辑，尤其服务 `github-watch` 这类 stateful lane。

### 推荐 CLI 形态（第一版）

已拍板的一级命令树：

```bash
signal-engine collect
signal-engine diagnose
signal-engine status
signal-engine lanes
signal-engine config
```

推荐交互形态：

```bash
signal-engine collect --lane github-watch
signal-engine collect --lane x-feed --date 2026-04-06
signal-engine diagnose --lane github-watch
signal-engine status --lane github-watch --date 2026-04-06
signal-engine lanes list
signal-engine config check
```

当前倾向：
- `collect / diagnose / status` 是一级动作
- `lane` 是目标对象

### `run.json`（第一版最小字段）

按 lane-run 粒度写，例如：
- `signals/github-watch/2026-04-06/run.json`
- `signals/x-feed/2026-04-06/run.json`

第一版最小字段已拍板为：
- `lane`
- `date`
- `status`
- `started_at`
- `finished_at`
- `warnings`
- `errors`
- `summary.repos_checked`
- `summary.signals_written`
- `summary.signal_types`
- `artifacts.index_file`
- `artifacts.signal_files`

### 第一批迁移建议

此前的难度排序仍然成立，但**第一批实施范围已收窄为：只迁 `x-feed`**。

当前迁移优先级参考：
1. `x-feed`（第一批）
2. `x-following`
3. `github-trending-weekly`
4. `github-watch`
5. `product-hunt-watch`

### 第一版先别做什么

- plugin system
- registry-driven dynamic loading
- processing/ranking layer
- 过重的平台化
- 过多输出格式
- lane auto-discovery

一句话：

> 先别做 opencli；先做一个稳的 Python collect CLI。

## 仍未确定的问题

### 近期待定
- 仓库路径 / repo 名称（代码仓而不是 Obsidian 项目名）
- 旧 `daily-lane` 与新项目的过渡方式
- `x-feed` 第一批迁移时，旧输出协议需要 100% 兼容到什么程度
- `diagnose / status / config-check / mock` 四者的第一版详细边界

### 中期待定
- `x-following` 何时进入第二批迁移
- `github-trending-weekly` 何时进入后续迁移
- `github-watch` 何时作为核心 stateful lane 迁移
- 是否以及何时引入 command registry / adapter metadata

### 明确暂缓
- processing layer 何时启动
- filtering / dedupe / ranking 的正式落地时机
- 总 run manifest
- attempts 历史
- plugin / dynamic loading / 过重平台化能力
