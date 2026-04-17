---
title: Feishu / lark-cli 给 MT 发消息（send as user）方法卡
tags:
  - feishu
  - lark-cli
  - MT
  - Rook
  - send-as-user
---

# Feishu / lark-cli 给 MT 发消息（send as user）方法卡

## 这张卡解决什么问题

当 Rook 需要通过飞书把消息发进 **MT 的真实历史 p2p 会话** 时，不走当前 Rook DM 线程，而是走 `lark-cli im +messages-send --as user` 这条链路。

这条方法已经实测通过。

## 关键识别值

### MT 真实历史会话
- `chat_id`: `oc_b3c40399b83d73e55b604309e886796a`
- `open_id`: `ou_ebf786ff86b8c8d3c8d0b830706fc376`

### 当前 Rook 会话（不要混淆）
- `chat_id`: `oc_7da2b33bca4bfaa5aa20021b69e4a25f`

## 前置检查

```bash
command -v lark-cli
lark-cli auth status
lark-cli auth check --scope "im:message.send_as_user"
```

验收标准：
- `lark-cli` 已安装
- 当前登录身份是 `王贵平`
- `im:message.send_as_user` scope 已具备

## 最重要的纪律

这条链路走的是：
- **transport identity**：贵平的 Feishu 用户账号
- **author identity**：Rook

所以发给 MT 时，**必须显式声明这不是贵平本人直发**。

固定前缀：

> 我不是贵平，我是 Rook。

不要省略。

## 默认模板

```text
我不是贵平，我是 Rook。

[消息类型：同步 / 提问 / 请求验证 / 回执]
[目标：一句话说明]

正文：
{{BODY}}
```

## 发送命令

```bash
lark-cli im +messages-send \
  --as user \
  --chat-id "oc_b3c40399b83d73e55b604309e886796a" \
  --text "我不是贵平，我是 Rook。\n\n[消息类型：同步]\n[目标：一句话说明]\n\n正文：\n这里是具体内容。"
```

## 回读验证

```bash
lark-cli im +chat-messages-list \
  --as user \
  --chat-id "oc_b3c40399b83d73e55b604309e886796a" \
  --page-size 5 \
  --format json
```

成功标志：
- 最新消息内容匹配
- `sender.sender_type = user`
- `sender.name = 王贵平`

## 已验证样例

### 测试文本
- `MT user-send real target test 2026-04-17 22:27`

### 返回结果
- `message_id`: `om_x100b511060db94a4b2bcda2065da50a`
- `identity`: `user`
- `ok`: `true`

### 对端确认
MT 已回复确认收到，说明消息确实进入了 MT 的真实会话。

## 踩坑记录

### 1. `+messages-send` 不支持 `--format`
如果误加 `--format`，命令会直接报错；这不是消息发送失败，而是参数写错。

### 2. 不要把 MT 会话和当前 Rook 会话混淆
- MT：`oc_b3c40399b83d73e55b604309e886796a`
- Rook 当前 DM：`oc_7da2b33bca4bfaa5aa20021b69e4a25f`

## 一句话总结

后面如果 Rook 需要通过飞书给 MT 发消息，默认用这条链路：

- `lark-cli`
- `--as user`
- 目标 `chat_id = oc_b3c40399b83d73e55b604309e886796a`
- 消息开头必须写：`我不是贵平，我是 Rook。`
- 发完立刻用 `+chat-messages-list` 回读验证
