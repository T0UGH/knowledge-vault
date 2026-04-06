---
title: Rook AGM 安装指南
date: 2026-04-06
tags:
  - agm
  - agent-git-mail
  - rook
  - openclaw
aliases:
  - Rook AGM 安装手册
  - Rook Agent Git Mail Setup
---

# Rook AGM 安装指南

这份文档是给 **Rook** 的落地安装手册，目标不是“把命令跑完”，而是把 **AGM CLI + profile 配置 + daemon + 通讯录** 一次装对。

> [!important]
> **如果机器上装过老版本 AGM，先做旧状态检查，再做新安装。**
> 今天已经验证过：老版本残留的 config、launchd job、甚至旧 plugin/skill 安装痕迹，都可能让新版看起来“像装好了，其实没收死”。

## 目标状态

安装完成后，Rook 这台机器应满足：

1. `agm` 为最新版
2. 本机存在 `rook` 对应 profile
3. daemon 可正常运行
4. 通讯录里至少有：
   - `mt`
   - `leo`
5. `agm doctor --profile rook` 不应有 fail

---

## 1. 先检查旧版本残留

### 1.1 检查当前 agm 安装来源

```bash
which -a agm
agm --help
node -p "require('/usr/local/lib/node_modules/@t0u9h/agent-git-mail/package.json').version" 2>/dev/null || true
```

如果机器上已经有 `agm`，先不要急着覆盖，先继续检查下面几项。

### 1.2 检查旧配置目录

```bash
ls -la ~/.config/agm 2>/dev/null || true
find ~/.config/agm -maxdepth 2 -type f 2>/dev/null | sort || true
```

重点看是否存在这些旧状态：

- `~/.config/agm/config.yaml`
- `~/.config/agm/events.jsonl`
- `~/.config/agm/session-bindings.json`
- `~/.config/agm/state/`

### 1.3 检查旧 repo 目录

```bash
find ~/.agm -maxdepth 3 -type d 2>/dev/null | sort || true
find ~/.local/share/agm -maxdepth 3 2>/dev/null | sort | head -200 || true
```

重点区分两类目录：

- **旧布局**：`~/.agm/<agent>`
- **新布局**：`~/.agm/profiles/<profile>/self`

如果两套目录同时存在，说明这台机器很可能经历过版本迁移，后面安装时必须小心，不要误把旧目录当成新 self repo。

### 1.4 检查旧 daemon / launchd 残留

```bash
find ~/Library/LaunchAgents -maxdepth 1 -type f | grep -i agm || true
launchctl list | grep -i agm || true
launchctl list ai.agm.daemon.rook 2>&1 || true
```

如果已经存在 `ai.agm.daemon.rook` 或其他 AGM job，先记住，后面安装前要确保它们不是旧状态卡住。

### 1.5 检查旧 plugin / skill 残留

AGM 本体是 CLI + daemon；OpenClaw 侧的 skill/plugin 只是可选集成，不是 transport 真相。

但如果机器上残留过旧的 AGM skill/plugin，也要先确认，不要让它和新版 CLI 心智混在一起。

建议检查：

```bash
find ~/.openclaw -maxdepth 4 \( -iname '*agm*' -o -iname '*agent-git-mail*' -o -iname '*mailbox*' \) 2>/dev/null | sort || true
find ~/.claude -maxdepth 4 \( -iname '*agm*' -o -iname '*agent-git-mail*' -o -iname '*mailbox*' \) 2>/dev/null | sort || true
```

重点不是“全部删光”，而是确认：

- 有没有旧的 `agm-mail` skill / plugin
- 有没有旧脚本还在假定老 config schema
- 有没有旧 heartbeat / automation 仍在调用旧命令

> [!warning]
> 如果你看到旧插件逻辑还在写死老路径、老 config、老命令，不要直接复用。
> **先把 AGM CLI 安装闭环跑通，再决定是否重新接入 plugin。**

### 1.6 先备份旧状态

如果前面任一项存在旧数据，先备份：

```bash
TS=$(date +%Y%m%d-%H%M%S)
BACKUP_DIR=~/.agm-backups/$TS
mkdir -p "$BACKUP_DIR"
[ -d ~/.config/agm ] && cp -R ~/.config/agm "$BACKUP_DIR/config-agm"
[ -d ~/.agm ] && cp -R ~/.agm "$BACKUP_DIR/dot-agm"
[ -d ~/.local/share/agm ] && cp -R ~/.local/share/agm "$BACKUP_DIR/local-share-agm"
echo "$BACKUP_DIR"
```

---

## 2. 安装最新版 AGM CLI

```bash
npm install -g @t0u9h/agent-git-mail
```

安装后验证：

```bash
agm --help
node -p "require('/usr/local/lib/node_modules/@t0u9h/agent-git-mail/package.json').version"
```

预期：版本应为当前最新发布版本。

---

## 3. 为 Rook 建立 profile

这里默认：

- profile 名：`rook`
- self id：`rook`
- self remote repo：`https://github.com/agent-git-mail-group/rk-mail.git`

> [!note]
> 这里的 **Rook = rk**。
> mailbox remote 目前仍使用 `rk-mail.git`，只是本地 profile / 通讯录语义统一叫 `rook`。

先 dry-run：

```bash
agm bootstrap \
  --profile rook \
  --self-id rook \
  --self-remote-repo-url https://github.com/agent-git-mail-group/rk-mail.git \
  --dry-run
```

如果 dry-run 看起来正常，再正式执行：

```bash
agm bootstrap \
  --profile rook \
  --self-id rook \
  --self-remote-repo-url https://github.com/agent-git-mail-group/rk-mail.git
```

安装完成后检查：

```bash
agm config show --profile rook
```

---

## 4. 手工检查并修正 profile 配置

bootstrap 完成后，打开配置文件：

```bash
cat ~/.config/agm/config.yaml
```

最终至少应该有一段类似内容：

```yaml
profiles:
  rook:
    self:
      id: rook
      remote_repo_url: https://github.com/agent-git-mail-group/rk-mail.git
    contacts: {}
    notifications:
      default_target: main
      bind_session_key: null
    runtime:
      poll_interval_seconds: 30
```

### 4.1 建立通讯录：MT + Leo

这一步不要省。

把 `contacts` 改成至少包含这两个：

```yaml
profiles:
  rook:
    self:
      id: rook
      remote_repo_url: https://github.com/agent-git-mail-group/rk-mail.git
    contacts:
      mt:
        remote_repo_url: https://github.com/agent-git-mail-group/mt-mail.git
      leo:
        remote_repo_url: https://github.com/agent-git-mail-group/leo-mail.git
    notifications:
      default_target: main
      bind_session_key: null
    runtime:
      poll_interval_seconds: 30
```

> [!important]
> `rook` 安装好以后，**通讯录里必须有 MT 和 Leo**。
> 否则这套系统虽然能本地启动，但协作网络没建立起来，等于只装了半套。

### 4.2 如果需要飞书唤醒，再补 activation

如果 Rook 这台机器需要通过飞书唤醒 agent，再补上：

```yaml
activation:
  enabled: true
  activator: feishu-openclaw-agent
  dedupe_mode: filename
  feishu:
    open_id: <ROOK_OPEN_ID>
```

如果暂时不配 activation，也可以先把 transport 跑通。

---

## 5. 检查 self repo 路径是否正确

新版 AGM 的 self repo 默认路径是：

```bash
~/.agm/profiles/rook/self
```

检查：

```bash
git -C ~/.agm/profiles/rook/self status --short --branch
find ~/.agm/profiles/rook/self -maxdepth 1 -mindepth 1 | sort
```

预期：

- 这是一个 git repo
- 有 `inbox/`、`outbox/`、`archive/`

> [!warning]
> 如果你发现 `~/.agm/profiles/rook/self` 存在、但不是 git repo，说明这里有半成品残留。
> 不要硬接着用。先把目录挪走，再重新 bootstrap。

例如：

```bash
mv ~/.agm/profiles/rook/self ~/.agm/profiles/rook/self.pre-bootstrap-$(date +%Y%m%d-%H%M%S)
```

然后重新执行 bootstrap。

---

## 6. 启动 daemon

### 6.1 先看状态

```bash
agm daemon status --profile rook
```

### 6.2 启动

macOS 上建议用 launchd：

```bash
agm daemon start --profile rook
agm daemon status --profile rook
```

预期：

- `state: running`
- 能看到 pid

必要时进一步确认：

```bash
launchctl list ai.agm.daemon.rook 2>&1 || true
```

### 6.3 如果之前装过旧版，强烈建议先 stop 再 start

```bash
agm daemon stop --profile rook || true
agm daemon start --profile rook
```

原因：旧版机器上可能有 loaded-but-stopped 的旧 job 卡住；先 stop 再 start 更稳。

---

## 7. 跑 doctor

```bash
agm doctor --profile rook
```

理想状态：

- `fail=0`
- `warn` 最多只剩 activation / first-run 类提醒

常见可接受的 warn：

- `last_activation`：最近没有 activation 事件
- `activation_state`：首次运行或尚未触发 activation
- `waterline_ref`：首次运行还没建立水位线

但如果有以下问题，就不要继续往后：

- `git_is_repo = FAIL`
- `git_origin_matches_config = FAIL`
- `daemon_recent = FAIL`

---

## 8. 最小联调验证

### 8.1 先看通讯录是否生效

```bash
agm config show --profile rook
```

确认 `contacts` 里至少有：

- `mt`
- `leo`

### 8.2 测试读取对方 mailbox

```bash
agm list --profile rook --agent mt --dir inbox --format table
agm list --profile rook --agent leo --dir inbox --format table
```

如果第一次访问，会触发 contact cache / clone，这是正常的。

### 8.3 发送一封测试信给 MT

先准备 body：

```bash
cat > /tmp/rook-agm-test.md <<'EOF'
Rook AGM 已完成安装测试。

- profile: rook
- contacts: mt, leo
- daemon: running
EOF
```

发送：

```bash
agm send \
  --profile rook \
  --from rook \
  --to mt \
  --subject "rook agm setup test" \
  --body-file /tmp/rook-agm-test.md
```

如果这一步成功，说明：

- self repo 正常
- 联系人 remote 正常
- send path 正常

---

## 9. 如果安装异常，优先按这个顺序排查

1. **先查版本**：是不是还在跑旧 `agm`
2. **再查 config**：是不是还在用老 schema
3. **再查 repo 路径**：`~/.agm/profiles/rook/self` 是不是 git repo
4. **再查 daemon**：launchd job 是否卡在旧状态
5. **再查 plugin/skill**：有没有旧自动化还在调用老命令

建议命令：

```bash
which -a agm
agm --help
agm config show --profile rook
agm doctor --profile rook
agm daemon status --profile rook
launchctl list ai.agm.daemon.rook 2>&1 || true
```

---

## 10. 完成标准

满足以下条件，才算 Rook 这套 AGM 真正装好：

- `agm` 是最新版
- `rook` profile 已建立
- `~/.agm/profiles/rook/self` 是正常 git repo
- `contacts` 里有 `mt` 和 `leo`
- `agm daemon status --profile rook` 显示 `running`
- `agm doctor --profile rook` 无 fail
- 至少完成一次对 `mt` 的测试发送

> [!success]
> 真正的完成，不是“命令执行过”，而是 **profile、daemon、通讯录、最小联调** 全部闭环。

## 相关笔记

- [[2026-04-05-agm-v1.1.0-release-note]]
- [[2026-04-04-agm-daemon-lifecycle-launchd-design]]
- [[2026-04-05-agm-gap-analysis]]
