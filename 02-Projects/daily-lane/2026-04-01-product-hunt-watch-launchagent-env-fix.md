---
tags:
  - daily-lane
  - launchagent
  - product-hunt-watch
  - ops
source_repo: https://github.com/T0UGH/daily-lane
created: 2026-04-01
---

# 2026-04-01 product-hunt-watch LaunchAgent 环境变量修复

## 现象

`daily-lane` 配置里明明启用了 5 条 lane，但 2026-04-01 的最终 report 只有 4 条。
缺失的是 `product-hunt-watch`。

进一步排查发现：

- `~/.daily-lane-data/signals/product-hunt-watch/2026-04-01/index.md` 不存在
- `~/.daily-lane-data/logs/product-hunt-watch.log` 中明确出现：
  - `WARN: PH_API_TOKEN not set, skipping product-hunt-watch`

因此问题不在 Editor / Humanizer / report 层，而在 lane 采集层。

## 根因

机器本地 `~/.zshrc` 里虽然已经存在：

- `export PH_API_TOKEN=...`

但真实执行 `product-hunt-watch` 的入口并不是交互 shell，而是 macOS LaunchAgent：

- `~/Library/LaunchAgents/com.haha.daily-lane.product-hunt-watch.plist`

该 LaunchAgent 的 `EnvironmentVariables` 原先只显式注入了：

- `HOME`
- `PATH`
- `DAILY_LANE_DATA_DIR`

没有注入：

- `PH_API_TOKEN`

所以 LaunchAgent 启动 `./run.sh --lane product-hunt-watch --no-push` 时，lane 会因为缺 token 被静默 skip。

## 验证

### 直接按当前非交互路径运行

```bash
cd ~/workspace/daily-lane && ./run.sh --lane product-hunt-watch --date 2026-04-01 --no-push
```

结果：

- `WARN: PH_API_TOKEN not set, skipping product-hunt-watch`

### 先 source shell 环境再运行

```bash
source ~/.zshrc >/dev/null 2>&1
cd ~/workspace/daily-lane && ./run.sh --lane product-hunt-watch --date 2026-04-01 --no-push
```

结果：

- 正常抓取 Product Hunt API
- 成功产出 `signals/product-hunt-watch/2026-04-01/`
- 正常生成 `index.md`
- 正常退出 `code 0`

### 修复 live LaunchAgent 后再按 launchd 路径触发

修复方式采用更稳的 **方案 B**：不依赖 `~/.zshrc`，直接在 LaunchAgent 的 `EnvironmentVariables` 中显式注入 `PH_API_TOKEN`。

修复后通过：

```bash
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.haha.daily-lane.product-hunt-watch.plist
launchctl kickstart -k gui/$(id -u)/com.haha.daily-lane.product-hunt-watch
```

再次验证日志，已看到：

- `=== product-hunt-watch lane === date: 2026-04-01`
- `Fetched page 1: 20 posts`
- `Fetched page 2: 20 posts`
- `Fetched page 3: 20 posts`
- `=== Done: 39 signals from 3 topics ===`

说明 live 调度链路已恢复正常。

## 结论

这次问题的本质不是：

- `product-hunt-watch` 脚本坏了
- Product Hunt API 坏了
- report 漏写了一路

而是：

> **LaunchAgent 没有继承 shell profile 环境，导致 `PH_API_TOKEN` 未显式注入。**

## 后续原则

对类似由 LaunchAgent / cron / OpenClaw 非交互环境触发的任务：

- 不要假设 `~/.zshrc` / `~/.bashrc` 会自动生效
- 关键凭证优先显式写入调度入口的环境变量
- 出现“某条 lane 静默缺失”时，先检查当日 `signals/<lane>/<date>/index.md` 是否存在，再看对应日志，不要先怀疑 report prompt
