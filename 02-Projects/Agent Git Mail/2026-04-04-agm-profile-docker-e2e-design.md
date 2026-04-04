# AGM Profile Docker E2E 测试方案设计

> 状态：Draft Final  
> 目标边界：验证到 HappyClaw ingress 边界为止，不接真实 HappyClaw / Feishu / 本机常驻实例

## 1. 目标

为 AGM profile 模型设计一套**完全隔离的 Docker E2E 测试方案**，用于验证：

- profile config 生效
- self repo / contact cache / state 路径隔离正确
- daemon 能检测新信
- host adapter 能调用宿主 ingress
- ingress success 才写 checkpoint
- ingress failure 不写 checkpoint
- 双 profile（MT / Hex）共存时状态不串

这套 E2E 的验收终点是：

> **AGM daemon → host adapter → `/internal/agm/ingress`**

不要求覆盖：
- 真实 HappyClaw runtime 执行
- 真实 Feishu 回流
- 本机常驻 HappyClaw / OpenClaw 实例

---

## 2. 总体决策

## 2.1 必须在 Docker 中跑

本轮 E2E 必须使用 Docker 隔离环境，不能复用当前稳定环境。

明确禁止：

- 使用本机常驻 HappyClaw 作为验收宿主
- 使用本机常驻 OpenClaw 作为验收环境
- 直接复用当前真实 `~/.agm` / `~/.config/agm` 状态目录
- 让真实 Feishu 路由参与本轮验收

原因：

- 避免污染现有稳定环境
- 避免把历史状态误判成实现正确
- 保证测试可重复
- 保证后续可自动化、可迁移到 CI

---

## 2.2 宿主侧采用最小 fake ingress server

本轮不在 Docker 中运行完整 HappyClaw，而是运行一个**最小 fake ingress server**。

原因：

- 这轮 AGM profile 的验收边界只到 ingress
- 不需要把完整 HappyClaw runtime / Feishu 路由复杂度一起带入
- 更容易稳定自动化
- 失败定位更清楚：能直接区分是 AGM 问题还是宿主问题

---

## 2.3 用 docker-compose 组织场景

测试环境采用 `docker-compose`，而不是零散 `docker run`。

原因：

- 服务编排清楚
- 环境更稳定
- 更容易一键拉起/销毁
- 更适合后续固化为团队标准测试入口

---

## 3. 测试边界

## 3.1 本轮必须验证的内容

### AGM 侧
- `profiles.<name>` 配置结构可正常工作
- `--profile` 命令语义正确
- self repo 路径自动派生正确
- contact cache 路径自动派生正确
- state 目录按 profile 隔离
- daemon 只 watch 当前 profile self repo
- host adapter 按当前 profile 配置发请求
- checkpoint 绑定 ingress success
- ingress failure 不写 checkpoint

### 宿主侧
- `/internal/agm/ingress` endpoint 收到请求
- `X-Internal-Secret` header 校验通过/失败
- request body 结构符合预期
- success/failure 响应语义可控

---

## 3.2 本轮明确不验证的内容

以下内容不纳入本轮 E2E：

- 真实 HappyClaw runtime trigger
- 真实 Feishu DM 回流
- 真实 OpenClaw external activator
- 真实用户聊天路由
- 本机稳定环境联调

这些可以作为后续独立集成测试话题，但不属于本轮 AGM profile 模型 E2E 的必验范围。

---

## 4. 测试环境结构

## 4.1 docker-compose 服务

建议最小包含两个服务：

### service 1：`agm-test`
职责：
- 运行 AGM CLI / daemon / E2E 脚本
- 挂载测试 config / 测试 repo / 测试 state 目录
- 执行 success / failure 两条主用例

### service 2：`fake-ingress`
职责：
- 提供 `POST /internal/agm/ingress`
- 校验 auth header
- 校验 request body
- 记录收到的请求
- 按测试模式返回 success 或 failure

---

## 4.2 推荐目录结构

建议在 AGM 仓库中新增测试目录：

```text
packages/agm/test/docker/profile-e2e/
  docker-compose.yml
  run-e2e.sh
  assert-success.sh
  assert-failure.sh
  fixtures/
    config.yaml
    seed-repos.sh
    bootstrap-profiles.sh
  fake-ingress/
    Dockerfile
    server.js
    package.json
```

说明：

- `docker-compose.yml`：编排 `agm-test` + `fake-ingress`
- `run-e2e.sh`：统一拉起并执行测试
- `assert-success.sh`：验证 success path
- `assert-failure.sh`：验证 failure path
- `fixtures/`：测试配置与初始化脚本
- `fake-ingress/`：最小宿主实现

---

## 5. 配置与路径模型

## 5.1 Docker 内部路径原则

Docker 内不要使用宿主机真实：

- `~/.agm`
- `~/.config/agm`

而应使用容器内隔离目录，例如：

```text
/workspace/testdata/.agm/
/workspace/testdata/.config/agm/
```

并通过环境变量或测试配置确保 AGM 只使用这套测试路径。

---

## 5.2 Profile 测试数据

建议在测试中固定两个 profile：

- `mt`
- `hex`

配置结构示例：

```yaml
profiles:
  mt:
    self:
      id: mt
      remote_repo_url: /repos/mt-remote.git
    contacts:
      hex:
        remote_repo_url: /repos/hex-remote.git
    runtime:
      poll_interval_seconds: 1
    host_integration:
      kind: happyclaw
      happyclaw:
        base_url: http://fake-ingress:3000/internal
        bearer_token_env: HAPPYCLAW_INTERNAL_SECRET
        target_jid: feishu:test-hex

  hex:
    self:
      id: hex
      remote_repo_url: /repos/hex-remote.git
    contacts:
      mt:
        remote_repo_url: /repos/mt-remote.git
    runtime:
      poll_interval_seconds: 1
    host_integration:
      kind: happyclaw
      happyclaw:
        base_url: http://fake-ingress:3000/internal
        bearer_token_env: HAPPYCLAW_INTERNAL_SECRET
        target_jid: feishu:test-hex
```

说明：
- 这里的 `remote_repo_url` 可以在 Docker 测试场景中用本地裸仓路径或 file-based git remote 表示
- 重点不是模拟 GitHub，而是验证 mailbox 协议和路径/状态语义

---

## 6. fake ingress server 设计

## 6.1 最小接口

只实现：

```http
POST /internal/agm/ingress
```

---

## 6.2 需要验证的内容

### Header
- 必须有 `X-Internal-Secret`
- secret 错误时返回 401/403

### Body
至少校验：

```json
{
  "targetJid": "feishu:test-hex",
  "source": "agm",
  "sourceMeta": {
    "mailboxId": "hex",
    "messageId": "...",
    "from": "mt",
    "reason": "new_mail"
  },
  "content": "..."
}
```

### 响应
#### success path
```json
{
  "ok": true,
  "delivery": "ingressed",
  "targetJid": "feishu:test-hex",
  "effectiveGroupJid": "feishu:test-hex",
  "messageId": "fake-123"
}
```

#### failure path
```json
{
  "ok": false,
  "error": "forced_failure"
}
```

---

## 6.3 记录方式

fake ingress 必须把收到的请求记录下来，例如写到：

```text
/tmp/fake-ingress/requests.jsonl
```

这样测试脚本可以断言：
- 是否真的收到请求
- 收到几次
- payload 是否正确
- header/body 是否符合预期

---

## 7. 核心测试用例

## 7.1 用例 A：success path

### 目标
验证 ingress success 时：
- adapter 调用成功
- checkpoint 写入
- activation_sent 事件写入

### 步骤
1. 初始化 `mt` / `hex` profile 测试配置
2. 初始化 self repo 与 remote repo
3. 给 `hex` inbox 注入一封来自 `mt` 的新信
4. 启动：
   ```bash
   agm --profile hex daemon --once
   ```
5. fake ingress 返回 success
6. 断言：
   - fake ingress 收到 1 次请求
   - `targetJid` 正确
   - `source=agm`
   - `sourceMeta` 正确
   - `activation-state.json` 已写入
   - `events.jsonl` 出现 `activation_sent`

---

## 7.2 用例 B：failure path

### 目标
验证 ingress failure 时：
- checkpoint 不写
- activation_failed 事件写入

### 步骤
1. 用相同方式初始化 profile 与 repo
2. 给 `hex` inbox 注入一封新信
3. 启动：
   ```bash
   agm --profile hex daemon --once
   ```
4. fake ingress 返回 failure
5. 断言：
   - fake ingress 收到请求
   - `activation-state.json` **未写入该邮件**
   - `events.jsonl` 出现 `activation_failed`

---

## 7.3 用例 C：双 profile 隔离

### 目标
验证：
- `mt` / `hex` 的 state 不串
- self repo / contact cache 路径不串

### 断言
- `mt` 的 state 目录与 `hex` 不同
- `mt` 的 self repo 与 `hex` 不同
- `mt` 的 contact cache 与 `hex` 的 self repo 不同
- `hex` daemon 运行后，不会写 `mt` 的 checkpoint/state

---

## 8. 验收标准

## 8.1 通过标准
本轮 Docker E2E 通过，至少意味着：

1. `agm --profile <name>` 工作正常
2. profile config 解析正确
3. self repo / contact cache / state 路径隔离正确
4. daemon 能检测新信
5. host adapter 能调用 fake ingress
6. ingress success 才写 checkpoint
7. ingress failure 不写 checkpoint
8. 双 profile 状态不串

---

## 8.2 不可接受的伪通过
以下情况不能算通过：

- 用本机常驻 HappyClaw 冒充宿主
- 用真实 Feishu 路由冒充 ingress success
- 只验证 CLI 命令退出码，不验证 ingress side effect
- success / failure 只测一条
- profile 隔离没验证，只测单 profile

---

## 9. 实施建议

## 9.1 推荐先后顺序

建议按下面顺序推进：

1. 先做 `fake-ingress` 服务
2. 再做 Docker 内 profile 测试 repo 初始化脚本
3. 先跑 success path
4. 再跑 failure path
5. 最后补双 profile 隔离断言

这样可以最小化定位成本。

---

## 9.2 与当前 implementation plan 的关系

这份 Docker E2E 设计是对：

- `2026-04-04-agm-profile-model-implementation-plan-final.md`

中 **Phase 8 测试纪律** 的具体展开。

它不改变当前已拍板的实现边界，只是把：

> “E2E 必须在 Docker 隔离环境中执行”

落实成一套可操作的测试方案。

---

## 10. 最终建议

正式采用如下测试策略：

> **AGM profile 模型的 E2E 使用 docker-compose 编排；AGM 在 Docker 内运行；宿主侧使用最小 fake ingress server；验收终点为 AGM daemon → host adapter → `/internal/agm/ingress`。**

一句话总结：

> **这轮测的是 AGM 是否正确把 profile 语义和 ingress 语义收干净，不测真实 HappyClaw/Feishu 闭环。**
