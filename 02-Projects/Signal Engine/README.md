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

### 第一批迁移建议

1. `x-feed`
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

## 待定事项

- 仓库路径 / repo 名称
- CLI 命令结构细节
- 首批迁移 lane 的实施顺序
- 旧 `daily-lane` 与新项目的过渡方式
- processing layer 未来何时启动
