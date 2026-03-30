# OpenClaw / Rook 故障排查指南

## 症状与排查步骤

### 1. Rook 不回复消息

**排查命令**：

```bash
# 查看 OpenClaw gateway 状态
openclaw status

# 查看实时日志
openclaw logs | tail -50

# 关键看 error / warn / failover 关键字
openclaw logs 2>&1 | grep -E "error|warn|failover|rate.limit|timeout"
```

**常见原因**：

| 原因 | 日志关键词 | 解决方案 |
|------|-----------|---------|
| Gemini API 配额耗尽 | `rate_limit`、`429` | 切换到其他模型（OpenAI/MiniMax） |
| GPT-5.4 网络超时 | `fetch failed`、`timeout` | 检查本机到 OpenAI API 连通性 |
| Session 上下文满 | `178k/200k (89%)` | 重置 session 文件 |
| Session 锁死 | `.lock` 文件残留 | 删除锁文件，重置 session |

### 2. 查看当前 Session 状态

```bash
openclaw status | grep -A15 "Sessions"
```

输出示例：
```
│ agent:main:feishu:direct:ou_255…  │ direct │ 4m ago │ gpt-5.4 │ 178k/200k (89%) │
```

**高危信号**：token 占用 > 80%，容易卡死。

### 3. 查看认证状态

```bash
openclaw auth check 2>&1
```

### 4. 重置卡死的 Session

Session 文件位置：`~/.openclaw/agents/main/sessions/sessions.json`

找到卡死的 session 对应的 `sessionId`，然后：

```bash
# 找到 session 文件
SESSION_ID="<session-id>"
SESSION_FILE="$HOME/.openclaw/agents/main/sessions/${SESSION_ID}.jsonl"

# 重命名（备份）
mv "$SESSION_FILE" "$SESSION_FILE.reset"

# 删除 sessions.json 中的条目（用 python3）
python3 -c "
import json
with open('$HOME/.openclaw/agents/main/sessions/sessions.json', 'r') as f:
    data = json.load(f)
key = 'agent:main:feishu:direct:<user-id>'
if key in data:
    del data[key]
with open('$HOME/.openclaw/agents/main/sessions/sessions.json', 'w') as f:
    json.dump(data, f)
print('Done')
"

# 重启 gateway
openclaw gateway restart
```

### 5. 切换默认模型

配置文件：`~/.openclaw/openclaw.json`

修改 `agents.defaults.model`：

```json
"model": {
  "primary": "openai-codex/gpt-5.4",
  "fallbacks": [
    "minimax-cn/MiniMax-M2.7-highspeed",
    "glm/glm-5"
  ]
}
```

验证并重启：
```bash
openclaw config validate && openclaw gateway restart
```

### 6. 查看模型配额（Google AI）

访问：https://ai.dev/rate-limit

---

## 日志中的关键错误模式

| 日志模式 | 含义 |
|---------|------|
| `rate limit reached. 429` | API 配额超限，模型不可用 |
| `fetch failed. timeout` | 网络连接 OpenAI/Google 失败 |
| `LLM request timed out` | 模型响应超时 |
| `lane task error` | Session 执行出错，通常是上游 API 问题 |
| `FailoverError` | 主模型失败，尝试 fallback |
| `typing TTL reached` | 飞书 typing indicator 消失，说明模型开始响应 |
| `.lock` 文件残留 | Session 被锁，无 agent 进程持有 |

## 进程管理

```bash
# 查看 OpenClaw gateway 进程
ps aux | grep openclaw-gateway | grep -v grep

# 重启 gateway
openclaw gateway restart

# 查看所有 agent session
openclaw sessions --agent main
```
