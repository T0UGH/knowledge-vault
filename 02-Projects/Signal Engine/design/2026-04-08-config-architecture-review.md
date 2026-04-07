# Signal Engine 配置架构 Review

> Date: 2026-04-08
> Source: signals-engine repo (https://github.com/T0UGH/signals-engine)

## 当前配置架构

### 配置来源

- **配置文件**：`~/.daily-lane/config/lanes.yaml`（项目外部）
- **加载路径**：`commands/collect.py` 的 `load_config()`
  - 默认：`~/.daily-lane/config/lanes.yaml`
  - 可通过 `--config` 参数覆盖
  - 可通过 `DAILY_LANE_CONFIG` 环境变量覆盖
- **数据目录**：默认 `~/.daily-lane-data`，可通过 `--data-dir` 或 `DAILY_LANE_DATA_DIR` 覆盖

### 各 Lane 配置字段

#### `x-feed`
```yaml
lanes["x-feed"]["source"]["auth"]["cookie_file"]    # string, default: ~/.signal-engine/x-cookies.json
lanes["x-feed"]["source"]["limit"]                  # int, default: 100
lanes["x-feed"]["source"]["timeout_seconds"]        # int, default: 30
```

#### `x-following`
```yaml
lanes["x-following"]["source"]["auth"]["cookie_file"]  # string
lanes["x-following"]["source"]["limit"]                # int, default: 200
lanes["x-following"]["source"]["timeout_seconds"]      # int, default: 30
```

#### `github-watch`
```yaml
lanes["github-watch"]["repos"]                               # list[string], 必填
lanes["github-watch"]["signals"]["release"]["enabled"]        # bool, default: true
lanes["github-watch"]["signals"]["release"]["lookback_days"]  # int, default: 7
lanes["github-watch"]["signals"]["release"]["max_per_repo"]  # int, default: 3
lanes["github-watch"]["signals"]["changelog"]["enabled"]    # bool, default: true
lanes["github-watch"]["signals"]["changelog"]["files"]       # list[string], default: [CHANGELOG.md]
lanes["github-watch"]["signals"]["readme"]["enabled"]        # bool, default: true
```

#### `github-trending-weekly`
```yaml
lanes["github-trending-weekly"]["trending"]["url"]       # string, default: GitHub trending URL
lanes["github-trending-weekly"]["trending"]["max_repos"] # int, default: 30
lanes["github-trending-weekly"]["readme"]["enabled"]     # bool, default: true
lanes["github-trending-weekly"]["readme"]["max_size"]    # int, default: 102400
```

#### `product-hunt-watch`
```yaml
lanes["product-hunt-watch"]["api"]["token_env"]      # string, 环境变量名, default: PH_API_TOKEN
lanes["product-hunt-watch"]["api"]["lookback_days"]   # int, default: 1
lanes["product-hunt-watch"]["api"]["max_pages"]       # int, default: 3
lanes["product-hunt-watch"]["api"]["max_per_topic"]  # int, default: 20
lanes["product-hunt-watch"]["topics"]                # list[string], 必填
```

---

## 观察到的模式与问题

### 模式
| 模式 | 说明 |
|------|------|
| 三层嵌套 | 几乎所有 lane 都是 `lanes.{lane}.source.auth.limit` 结构 |
| defaults 散落 | 每个 lane 在代码里自己写 default，config 文件里可以完全不写 |
| token 用 env ref | `product-hunt-watch` 用 `token_env` 环境变量名而非直接传 token |

### 问题

1. **config check 命令与实际加载路径不一致**
   - `commands/config.py` 写死查 `~/.daily-lane/config/lanes.yaml`
   - `collect.py` 还支持 `DAILY_LANE_CONFIG` 环境变量
   - 两者逻辑不统一

2. **无 schema 校验**
   - 字段写错静默失效，读到的是 None 或 default
   - 必填字段（如 `github-watch.repos`）没有校验

3. **load_config() 重复定义**
   - 定义在 `collect.py` 里，其他命令各自复制逻辑

4. **各 lane 独立读配置，无统一抽象**
   - 每个 lane 独立写 `ctx.config.get("lanes", {}).get("x-feed", {})`
   - 没有 `get_lane_config(ctx, "x-feed")` 之类的统一入口

---

## 待讨论方向

1. **统一 config 模块**：集中 load + 校验，给各 lane 提供类型安全读取
2. **schema 设计**：对每个 lane 配置做必填/可选校验？
3. **config 文件格式**：继续 YAML 还是换别的（TOML / JSON / env 文件）？
4. **defaults 管理**：defaults 放在 config 文件里还是代码里？

---

## 相关文件

- `src/signals_engine/commands/collect.py` — load_config() 入口
- `src/signals_engine/commands/config.py` — config check 命令
- `src/signals_engine/core/context.py` — RunContext，config 传入点
