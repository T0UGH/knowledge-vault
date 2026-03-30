# 2026-03-30 AGM + OpenClaw Plugin（Docker）验证进展

## 最新结论

截至目前，`agent-git-mail` 的 **MVP 可以认为已经打通**。

更准确地说：

1. **AGM 本体 MVP 已通过**
   - 在 Docker + 两个真实 GitHub 私有测试仓库下，已经验证通过：
     - `send`
     - `read/list`
     - `reply`
     - `archive`
     - remote fresh clone 一致性

2. **OpenClaw plugin MVP 已通过**
   - plugin 已验证到真实 session transcript，而不只是 `enqueue_done`
   - 关键路径已通：
     - 新信出现
     - AGM plugin 检测到新信
     - `enqueueSystemEvent`
     - `requestHeartbeatNow()`
     - heartbeat session 消费 system event
     - session transcript 中出现 AGM 通知文本

## 当前关键实现结论

本次 Docker 内最终打通的关键，不是继续伪造 session entry，而是：

- 放弃 `agent:test:dummy` 这种 dummy session
- 改为把 `AGM_FORCED_SESSION_KEY` 指向：

```text
agent:main:main
```

也就是 OpenClaw 的 heartbeat session。

原因：
- dummy session 只有 session entry，没有 active runtime
- heartbeat session 有真实 runtime
- `requestHeartbeatNow()` 能真正触发消费 system event

## 当前 MVP 路由策略（已更新）

当前实现已从“测试期强制写死 main”进一步收口为：

1. 如果配置了 `AGM_FORCED_SESSION_KEY` → 用显式配置
2. 否则如果有 runtime session binding → 用 binding
3. 再否则默认回退到：

```text
agent:main:main
```

也就是说，MVP 现在的正式策略已经变成：

> **默认发到 main session，但保留配置覆盖能力。**

这比之前单纯依赖 forced session key 更接近真实用户可用形态，也更符合后续产品化方向。

## 已拿到的有效证据

Hex 最终交付的关键 transcript 证据：

```text
USER: System: [2026-03-30 05:29:52 UTC] New agent git mail: from=unknown, file=h2-mail-2026-03-30T05-29-28Z.txt
ASSISTANT: HEARTBEAT_OK
```

这说明：
- AGM 通知已经成功进入真实 session transcript
- 不是只停留在 plugin log / in-memory queue / enqueue_done

## 当前可以下的工程判断

### 可以明确说成立的

> `agent-git-mail` 的 MVP 已经成立：
> - 邮件系统本体可用
> - OpenClaw plugin 可用
> - Docker 验证已通到真实 session transcript

### 需要补充说明的边界

当前 plugin 的可见性链路是通过 **heartbeat session (`agent:main:main`)** 实现的，而不是单独的 AGM 专用会话。

这不是 blocker，但属于当前实现特征：
- 说明 MVP 已可用
- 也说明后续如果要产品化，可以再考虑是否需要 AGM 专用 session / 更明确的通知消费入口

## 现阶段最合理的结论

> **MVP 已搞定。**
>
> 后续工作不再是“能不能用”，而是“要不要把 heartbeat session 方案产品化/规范化，以及是否需要更独立的通知消费路径”。

## 后续可能的优化项（不阻塞 MVP）

1. 把 Docker 测试环境中的 provider / gateway 配置固化到 `test-openclaw.json`
2. 评估是否要从 heartbeat session 演进到 AGM 专用 session
3. 优化通知文本中的 `from=unknown` 提取逻辑
4. 增加一键 doctor / self-check，验证插件安装后真实可见性
