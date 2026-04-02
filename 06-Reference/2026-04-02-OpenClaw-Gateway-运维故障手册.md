# OpenClaw Gateway 运维故障手册

> Hex 经验沉淀，2026-04-02

---

## 常用排查命令

```bash
# 健康检查
curl http://localhost:18789/health

# 实时日志
tail -f /tmp/openclaw/openclaw-$(date +%Y-%m-%d).log

# 查看 gateway 进程
ps aux | grep openclaw-gateway

# 查看监听端口
lsof -p $(pgrep -f openclaw-gateway) -i -P -n

# 频道状态
openclaw channels status

# 查看 gateway 日志文件
openclaw logs
```

---

## 进程管理

### 正确重启方式

```bash
# ✅ 正确
openclaw daemon stop
openclaw daemon start

# ❌ 错误 - restart 命令不存在
openclaw restart

# ❌ 危险 - 会杀掉 OrbStack/Docker 网络代理
lsof -ti:18789 | xargs kill

# ✅ 安全 - 仅杀监听进程
lsof -ti:18789 -sTCP:LISTEN | xargs kill
```

### LaunchAgent 管理

```bash
openclaw daemon install   # 安装 LaunchAgent
openclaw daemon start    # 启动
openclaw daemon stop     # 停止
openclaw daemon restart  # 重启
```

---

## 故障排查矩阵

### 1. Agent 重复回话 / 响应卡死

**症状**：飞书收到消息 → agent 开始 typing → 2分钟后停止，无回复

**排查步骤**：
1. `tail -f` 查看日志，确认消息是否被 `dispatching to agent`
2. 检查是否有 session lock：`ls ~/.openclaw/agents/main/sessions/*.lock`
3. 检查 lock 文件中的 PID 是否存在：`ps aux | grep <PID>`
4. 若 PID 已不存在或进程卡死 → 解锁 session

**Session 解锁操作**：
```bash
SESSION_ID="<uuid>"
# 将 session 文件重命名为 .stuck
mv ~/.openclaw/agents/main/sessions/${SESSION_ID}.jsonl \
   ~/.openclaw/agents/main/sessions/${SESSION_ID}.jsonl.stuck.$(date +%Y%m%d%H%M%S)
mv ~/.openclaw/agents/main/sessions/${SESSION_ID}.jsonl.lock \
   ~/.openclaw/agents/main/sessions/${SESSION_ID}.jsonl.lock.stuck.$(date +%Y%m%d%H%M%S)
```

### 2. Plugin 轮询报错（poll_error）

**症状**：日志中每30秒出现一次 `Cannot find module './watch-agent.js'` → poll_error

**根因**：旧 plugin 已被废弃/删除，但仍在 `openclaw.json` 的 `plugins.installs` 中注册

**排查**：
```bash
grep "poll_error" /tmp/openclaw/openclaw-$(date +%Y-%m-%d).log
```

**修复**：
1. 打开 `~/.openclaw/openclaw.json`
2. 删除 `plugins.installs` 中对应的旧 plugin 条目
3. `openclaw daemon restart`

### 3. 飞书 WebSocket 假性存活

**症状**：`openclaw channels status` 显示 feishu running，但消息收发不到

**根因**：WebSocket 连接实际已断开，状态未同步

**排查**：
```bash
tail -f /tmp/openclaw/openclaw-$(date +%Y-%m-%d).log
# 观察是否有 "ws client ready" 和 "feishu[default]: received message"
```

**修复**：
```bash
openclaw daemon restart
```

### 4. Gateway 端口被占用

**症状**：`Gateway failed to start: another gateway instance is already listening`

**排查**：
```bash
lsof -i :18789 -sTCP:LISTEN
```

**修复**：
```bash
openclaw daemon stop
# 确认进程已停
ps aux | grep openclaw-gateway
```

---

## 配置结构参考

### openclaw.json plugins 部分（正确状态）

```json
"plugins": {
  "load": {
    "paths": []
  },
  "entries": {
    "minimax-portal-auth": { "enabled": true },
    "feishu": { "enabled": true }
  },
  "installs": {
    "feishu": { ... }
    // ⚠️ 不应包含已废弃的 plugin
  }
}
```

---

## 关键路径

| 用途 | 路径 |
|------|------|
| 配置文件 | `~/.openclaw/openclaw.json` |
| 日志文件 | `/tmp/openclaw/openclaw-<date>.log` |
| Agent 会话 | `~/.openclaw/agents/main/sessions/` |
| 飞书插件 | `~/.openclaw/extensions/feishu/` |
| Gateway workspace | `~/.openclaw/workspace/` |

---

## 相关经验

- [[2026-03-29-agm-docker-build-fix]] — AGM Docker 镜像构建修复
