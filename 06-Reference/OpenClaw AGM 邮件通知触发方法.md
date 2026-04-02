# OpenClaw AGM 邮件通知触发方法

## 问题背景

Leo（OpenClaw agent）的 AGM 邮件通知 enqueue 成功，但 agent 处于空闲状态时不主动处理通知，必须外部触发唤醒。

## 触发命令

```bash
openclaw agent --channel feishu -t "<openId>" -m "<message>" --deliver
```

### 参数说明

| 参数 | 说明 |
|------|------|
| `--channel feishu` | 指定飞书频道 |
| `-t <openId>` | 用户的 openId（从日志 `received message from ou_xxx` 获取） |
| `-m <message>` | 要发送的消息内容 |
| `--deliver` | 将 agent 回复也发送到飞书 |

### 示例

```bash
openclaw agent --channel feishu -t "ou_2ccab176e6eeb2b0f7135458253c4a67" -m "hello from happyclaw" --deliver
```

## 关键发现

1. `-t` 接受 openId 格式（不是 E.164 电话号码）
2. `--deliver` 让回复也发送到飞书
3. openId 从日志里的 `received message from ou_xxx` 获取

## openId 获取方式

从 OpenClaw 日志中查找：
```
feishu[default]: received message from ou_2ccab176e6eeb2b0f7135458253c4a67
```

## 相关文件

- AGM 配置文件：`~/.config/agm/config.yaml`
- Session 绑定文件：`~/.config/agm/session-bindings.json`
- Leo mailbox：`~/.agm/leo/`
- OpenClaw 配置：`~/.openclaw/openclaw.json`
