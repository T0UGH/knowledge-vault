---
title: 新 Mac mini 复刻 OpenClaw / HappyClaw 环境清单（2026-03-31）
category: setup-note
tags:
  - OpenClaw
  - HappyClaw
  - Mac mini
  - environment
  - skills
  - cli
summary: 为新 Mac mini 快速复刻当前这台机器上的 OpenClaw 工作环境而写的交接文档，覆盖目录布局、关键 CLI、好用 skill、凭证位置、自动化链路和容易漏掉的设定。
---

# 新 Mac mini 复刻 OpenClaw / HappyClaw 环境清单（2026-03-31）

这篇不是“如何从零学习 OpenClaw”的教程，而是给未来的自己做的复刻清单。

目标很简单：

- 在新 Mac mini 上尽快搭起一套能用的 OpenClaw
- 旁边再配一套 HappyClaw
- 让日常工作流尽量无缝迁过去
- 少踩那些已经踩过的坑

我会优先写这台机器上**真正有用**的东西，而不是理论上可能有用的东西。

---

## 1. 先说当前机器的总体结构

当前主工作区在：

- `~/.openclaw/workspace`

这个目录不是随便放点文件，它实际上已经长成了整个 agent 工作台。

OpenClaw 相关核心目录包括：

- `~/.openclaw/agents/`：各 agent 的持久会话与状态
- `~/.openclaw/memory/`：OpenClaw memory 数据
- `~/.openclaw/logs/`：日志
- `~/.openclaw/cron/`：cron 运行记录
- `~/.openclaw/workspace/`：主工作区

当前工作区里比较关键的内容有：

- `docs/`：OpenClaw 本地文档镜像
- `github-daily/`：GitHub AI Daily 相关脚本/状态
- `memory/`：本地记忆文件
- `safe-claude/`
- `tmp/`
- `.git/`

单独拿出来说的几个外部仓库目录：

- `~/workspace/knowledge-vault`：Obsidian / GitHub memory 仓库
- `~/workspace/memory`：日报与相关脚本的重要仓库
- `~/workspace/daily-lane`
- `~/workspace/github-watch`
- `~/workspace/rk-memory`：Rook 核心记忆/配置备份仓

如果新机器要快速接手，**至少要把这些目录关系先搭对**，不然后面路径会一直错位。

---

## 2. 当前机器上值得优先装好的 CLI

我直接按“有用程度”列。

### 第一层：必须先有

#### 1) `openclaw`

当前路径：

- `/Users/haha/.homebrew/bin/openclaw`

这是主角，不解释。

建议新机器先确认：

```bash
which openclaw
openclaw status
```

当前这台机器上，`openclaw status` 的关键信息是：

- Gateway service 已装成 LaunchAgent，并处于 running
- Feishu channel 已配置可用
- heartbeat 已启用（30m）
- Memory plugin 已 ready

这意味着：**新机器不是只要能跑 CLI 就够了，还要把 gateway / channel / memory / LaunchAgent 都配起来。**

#### 2) `claude`

当前路径：

- `/Users/haha/.homebrew/bin/claude`

虽然 OpenClaw 是主平台，但本机协作里，Claude Code 仍然是更强的工程执行器。很多 coding / review / design 任务默认要敢于委给它。

#### 3) `node` / `pnpm`

当前路径：

- `node`：`~/.local/node-v22.19.0-darwin-arm64/bin/node`
- `pnpm`：`~/.local/node-v22.19.0-darwin-arm64/bin/pnpm`

OpenClaw、mcporter、opencli、xreach 这一串都离不开 Node 环境。

#### 4) `git`

不用多说，所有记忆仓库、日报仓库、配置备份都依赖它。

#### 5) `tmux`

当前路径：

- `/Users/haha/.homebrew/bin/tmux`

后面如果还要跑长期驻留的 Claude / 其他 agent，会用到。

---

### 第二层：高频、非常值得装

#### 6) `feishu-cli`

当前路径：

- `/usr/local/bin/feishu-cli`

这是这台机器上非常实用的一条链。它不只是“能发飞书消息”，而是已经进入实际工作流了：

- 上传日报到飞书文档
- 创建文档
- 给文档加权限
- 处理分享与协作

当前凭证位置：

- `~/.feishu-cli/config.yaml`

已知备注：

- 里面有 App ID / App Secret
- 遇到飞书上传或认证问题，先查这里
- 当前机器上曾成功上传文档并给用户 open_id 添加 `full_access`

如果新机器只装 OpenClaw 不装 `feishu-cli`，很多“最后一公里”体验会明显变差。

#### 7) `mcporter`

当前路径：

- `~/.local/node-v22.19.0-darwin-arm64/bin/mcporter`

这个很重要。当前机器上很多 MCP 相关排查、调用、登录态验证，都靠它。

尤其是小红书链路里，它已经是标准工具之一。

#### 8) `opencli`

当前路径：

- `~/.local/node-v22.19.0-darwin-arm64/bin/opencli`

当前机器上的一个明确原则是：

> 做网页/站点搜索和站点操作时，默认先检查 `opencli` 能不能直接解决，而不是先退回普通 web search。

适用场景包括：

- 搜站点
- 读网页
- 看某站有没有现成 CLI / 抓取入口
- 需要一个比“直接网页阅读”更干净稳定的路径

#### 9) `xreach`

当前路径：

- `~/.local/node-v22.19.0-darwin-arm64/bin/xreach`

这个是 X / Twitter 内容抓取链里很关键的一环。

尤其是 X Article 排查时，当前机器已经沉淀出方法：

- 先用 `xreach tweet <status-url> --json` 验证 status 是否可抓
- 再解 `t.co`
- 再判断是否跳到 `x.com/i/article/...`

不要跳过这一步。

#### 10) `uv`

当前路径：

- `~/.local/bin/uv`

如果你后面继续扩 Python 脚本链，它很省心，值得保留。

---

### 第三层：当前机器已存在的生态线索

#### 11) `happyclaw`

当前机器 `PATH` 里**没有**现成的 `happyclaw` 命令。

这点要特别记下来，免得未来自己以为“老机器上本来就装好了”。目前能确认的是：

- 你准备在新机器上走“一套 OpenClaw + 一套 HappyClaw”
- 但这台机器里，HappyClaw 并不是已经成熟沉淀成统一 CLI 的状态

所以新机器上搭 HappyClaw，应该把它当成一条**新增搭建任务**，不要误以为这里只是复制现成路径就行。

---

## 3. 当前机器上最好用、最值得保留的 skills

`~/.agents/skills/` 里现在有不少 skill，但真正高频、有明显复用价值的，我会先记这些。

### 过程类（最值钱）

#### `using-superpowers`

这是总入口。核心作用不是做具体工作，而是提醒 agent 先找对 skill，不要裸奔。

#### `systematic-debugging`

排故时很值钱。它强迫 agent 先做根因调查，不要一上来就乱改。

#### `test-driven-development`

如果要做正式代码改动，这是硬约束型 skill。

#### `verification-before-completion`

这个也很重要。它会逼着 agent 在说“好了”之前先拿出验证证据。

#### `writing-plans`

复杂任务先写计划，适合中型改动或多步实施。

---

### 产出类 / 工作流类

#### `agent-reach`

非常实用。需要搜索互联网内容、读链接、查平台内容时，它是现成好用的入口。

#### `humanizer-zh`

对中文润色很有用，尤其是把 AI 味明显的文本改得更像人写的。

#### `feishu-notify`

如果要把结果发到飞书群 / webhook，这个值得保留。

#### `baoyu-youtube-transcript`

拿 YouTube 字幕、封面时很好用。

#### `baoyu-url-to-markdown`

把网页转干净 Markdown，很适合资料沉淀。

#### `baoyu-format-markdown`

做格式化产出时省时间。

#### `baoyu-markdown-to-html`

后面要接公众号 / 飞书 / HTML 输出时会派上用场。

#### `baoyu-post-to-wechat`

如果新机器也要接公众号发布链，这个很值得保留。

---

## 4. 当前机器上已经形成的方法论 / 设定

这一部分比工具本身还重要。因为很多“好不好用”不是靠多装一个 CLI，而是靠已经形成了稳定习惯。

### 4.1 关于交付风格

当前协作默认不是：分析一通然后停下。

而是：

1. 先诊断
2. 直接修改
3. 测试 / try-run
4. 把经验固化到规则、配置、验证或文档里
5. 再汇报结果

这点要保留。否则新机器虽然装了一堆工具，最后产出手感还是会变差。

### 4.2 关于中文优先

默认输出应该是中文优先。不要把英语当默认交付语言。

### 4.3 关于知识沉淀

如果用户说“记住这个”“整理一下”“留档”，优先写进：

- `~/workspace/knowledge-vault`

而不是只留在 OpenClaw 的会话上下文里。

并且因为这个仓库还有别的 agent 会改，所以每次写之前要先：

```bash
git pull --rebase --autostash
```

写完再 commit / push。

### 4.4 关于 RK 自己的长期配置备份

Rook 的核心 Markdown 记忆 / 配置文件，权威目录仍然是：

- `~/.openclaw/workspace`

但要额外同步备份到：

- `~/workspace/rk-memory`

适用对象包括：

- `AGENTS.md`
- `SOUL.md`
- `USER.md`
- `MEMORY.md`
- `TOOLS.md`
- `HEARTBEAT.md`
- `memory/`

### 4.5 关于 Claude Code 的定位

当前机器已经明确：

> 本地 Claude Code 是更强的工程执行器。

也就是说：

- coding implementation
- technical design
- review

这些任务，该委就委，不要让 OpenClaw 硬扛所有实现工作。

### 4.6 关于 partner-comms

当前机器已经有一个长期的双 agent 通信根目录：

- `~/partner-comms/`

目录语义是发送者视角：

- `outbox/`
- `inbox/`
- `shared/`
- `archive/`

如果新机器还想保留 RK / Arc 这套分工，建议把这套目录结构一起复刻。

---

## 5. 当前机器已经接好的重要自动化链

### 5.1 飞书链

已经接好，至少包括：

- OpenClaw Feishu channel
- `feishu-cli`
- 飞书文档创建 / 导入 / 权限修改

这条链非常值得复刻，因为它把“产出 -> 交付”的路径缩短了很多。

### 5.2 日报链

当前机器上，OpenClaw 里已经有两条 08:30 的 cron：

- `x-ai-daily-0830`
- `github-ai-daily-0830`

相关工作区和数据目录包括：

- `~/workspace/daily-lane`
- `~/.daily-lane-data`

已知习惯：

- LaunchAgent 通过 `daily-lane/run.sh --lane <lane> --no-push` 执行
- 显式设置 `DAILY_LANE_DATA_DIR=/Users/haha/.daily-lane-data`
- 日志写到 `~/.daily-lane-data/logs/*.log`

### 5.3 小红书发布链

当前机器已经接好本地链路：

- `xiaohongshu-mcp`
- `mcporter`
- 登录态
- 发布脚本

关键数据目录：

- `~/.local/share/xiaohongshu-mcp/`

里面现在能看到：

- `cookies.json`
- `server.log`
- `server.pid`
- `login.log`
- `config/`

如果新机器要复刻这条链，至少要知道：

- 浏览器里能手动发，不等于 MCP 登录态有效
- 遇到问题先看 `server.log`
- 再用 `mcporter` 验证登录态

### 5.4 OpenClash / Claude 专用链

虽然这不属于 OpenClaw 本体，但它和日常 AI 工具可用性强相关，值得一起迁移思路。

当前机器最近沉淀出的关键结论：

- Claude 的网页/客户端异常时，不要先怀疑基础网络不通
- 先区分 fake-ip、本机解析、真实出口、公网画像
- `claude.ai -> 198.18.0.x` 可能只是 fake-ip，不是最终出口
- 当前目标链路是让 Claude 相关流量走美国静态 IP `107.180.144.40`

相关配置文档已在 Obsidian 里单独记录，不展开。

---

## 6. 新机器最容易漏掉的点

### 1) 只装 OpenClaw CLI，不装完整生态

这样最后会缺：

- `feishu-cli`
- `mcporter`
- `opencli`
- `xreach`
- `claude`
- `tmux`

很多工作流会变得“不完整但又不是完全不能用”，这种最烦。

### 2) 路径没对齐

尤其是：

- `~/.openclaw/workspace`
- `~/workspace/knowledge-vault`
- `~/workspace/rk-memory`
- `~/partner-comms`
- `~/.daily-lane-data`

这些路径如果随便换，后面很多脚本和记忆默认都会错。

### 3) 忘了同步凭证和登录态

重点检查：

- `~/.feishu-cli/config.yaml`
- OpenClaw channel / gateway 配置
- 小红书 MCP 登录态
- Claude / 代理相关环境变量

### 4) 忘了把 OpenClaw 服务装成 LaunchAgent

只在前台跑过一次 CLI，不等于长期环境搭好了。

### 5) 只复制代码，不复制方法论

真正好用的是：

- 中文优先
- 先排查再回抛问题
- 先 git pull 再写 knowledge-vault
- 交付前必须验证
- 该委托 Claude Code 就委托

这些比“多一个脚本”更影响手感。

---

## 7. 建议的新机器搭建顺序

我建议别一口气全装，按下面顺序最稳。

### Phase 1：先把基础平台拉起来

1. 装 `openclaw`
2. 跑 `openclaw status`
3. 配好 gateway / memory / Feishu channel
4. 建好 `~/.openclaw/workspace`
5. 把 `knowledge-vault`、`rk-memory`、`memory`、`daily-lane` 等仓库 clone 到正确位置

### Phase 2：把协作执行器和核心 CLI 补齐

1. 装 `claude`
2. 装 `node` / `pnpm`
3. 装 `tmux`
4. 装 `feishu-cli`
5. 装 `mcporter`
6. 装 `opencli`
7. 装 `xreach`
8. 装 `uv`

### Phase 3：恢复高价值工作流

1. 恢复 `knowledge-vault` 记忆沉淀链
2. 恢复 Feishu 文档创建 / 上传 / 权限链
3. 恢复 OpenClaw cron / daily-lane
4. 恢复小红书发布链
5. 恢复 RK / Arc 的 `partner-comms`

### Phase 4：最后再接 HappyClaw

因为当前机器上 HappyClaw 还不是现成完整状态，所以新机器上建议把它单独看成一个项目，不要和 OpenClaw 混成一步做。

---

## 8. 最后的判断

如果只是想“装一个能跑的 OpenClaw”，这件事并不难。

真正难的是把这台机器上已经慢慢长出来的这些东西一起复刻过去：

- 工具链
- 目录结构
- 记忆仓库
- 凭证
- 自动化
- 协作分工
- 交付风格

这些拼在一起，才是现在这套环境真正顺手的原因。

所以新 Mac mini 的目标不该只是“装好 OpenClaw”，而是：

> 复刻一整套能持续工作的 agent 环境。

如果后面真要开始迁移，建议直接以这篇为 checklist，一项项打勾，而不是靠记忆现想。
