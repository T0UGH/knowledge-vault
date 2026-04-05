# OpenClaw Dispatch 卡死问题调试：session 锁文件残留

## 日期
2026-04-05

## 问题现象
Rook（OpenClaw feishu direct session）收到消息后：
1. Feishu WebSocket 收到消息 ✓
2. Gateway dispatch 到 agent ✓
3. **Agent 进程从未启动**（无 claude 子进程）
4. 2 分钟后 typing indicator 超时消失
5. `dispatch complete` 从未出现
6. sessions.json 中 feishu direct session 的 `status` 为 `null`

## 根因
Session 锁文件残留（`{sessionId}.jsonl.lock`）导致 dispatch 机制认为 session 仍被占用，阻止新的 agent 进程启动。

具体流程：
1. 旧的 feishu direct session（6f010289）因上下文 92% 触发压缩
2. 压缩过程中产生 `.lock` 文件
3. 压缩 aborted（context 超限）
4. Session 从 sessions.json 中被清理，但 `.lock` 文件残留
5. 新消息到达时，gateway 发现 lock 文件存在，认为 session 仍活跃，拒绝启动新 agent
6. Dispatch 永远挂起

## 关键排查命令

```bash
# 查看 sessions.json 结构（注意：直接以 session key 为键，无嵌套 sessions{} 包装）
python3 -c "
import json
d = json.load(open('~/.openclaw/agents/main/sessions/sessions.json'))
v = d.get('agent:main:feishu:direct:ou_2555bf78b2fbb1599fc19a76317bbbd9', {})
print('sessionId:', v.get('sessionId'))
print('status:', v.get('status'))
"

# 查看锁文件
ls -la ~/.openclaw/agents/main/sessions/*.lock

# 查看实际运行的 claude 进程（确认是 OpenClaw 还是 HappyClaw）
ps aux | grep claude | grep -v grep
# 注意：OpenClaw 的 claude 父进程应该是 openclaw-gateway
# HappyClaw 的 claude 父进程是 ~/Downloads/happyclaw/container/agent-runner/dist/index.js

# OpenClaw status 概览
openclaw status

# 查看 gateway 日志
openclaw logs 2>/dev/null | grep -E "feishu.*received|dispatching|dispatch complete|prompt-error|aborted"
```

## sessions.json 结构（之前一直读错）

```json
{
  "agent:main:feishu:direct:ou_2555bf78b2fbb1599fc19a76317bbbd9": {
    "sessionId": "6f010289-2ec7-41e2-96a3-c44429845015",
    "updatedAt": 1775375278000,
    "status": null,
    ...
  }
}
```

**之前一直用 `d.get('sessions', {})` 读错了**，实际上 key 直接就是 session key，没有嵌套。

## 修复步骤

1. **删除锁文件**
```bash
rm -f ~/.openclaw/agents/main/sessions/{sessionId}.jsonl.lock
```

2. **从 sessions.json 移除残留 entry**（否则 gateway 不会重新创建干净 session）
```python
python3 -c "
import json
d = json.load(open('~/.openclaw/agents/main/sessions/sessions.json'))
key = 'agent:main:feishu:direct:ou_2555bf78b2fbb1599fc19a76317bbbd9'
if key in d:
    del d[key]
    json.dump(d, open('~/.openclaw/agents/main/sessions/sessions.json','w'), indent=2)
"
```

3. **重启 gateway**
```bash
launchctl kickstart -kp gui/$(id -u)/ai.openclaw.gateway
```

## 预防建议
- 上下文超过 90% 时手动开新 session，避免自动压缩失败
- 定期检查 `~/.openclaw/agents/main/sessions/*.lock` 是否有残留

## 相关文件
- sessions.json: `~/.openclaw/agents/main/sessions/sessions.json`
- session files: `~/.openclaw/agents/main/sessions/{id}.jsonl`
- lock files: `~/.openclaw/agents/main/sessions/{id}.jsonl.lock`
