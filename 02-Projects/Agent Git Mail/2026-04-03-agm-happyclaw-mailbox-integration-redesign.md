# AGM × HappyClaw Mailbox Integration Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 让 HappyClaw 也能稳定使用 AGM 邮件系统：AGM 负责 mailbox transport + daemon；HappyClaw 负责把新邮件信号送进正确主会话，并确保该信号像真实用户消息一样足以触发 agent。

**Architecture:** 保持 AGM 为独立 mailbox 系统，不把 mailbox 业务塞进 HappyClaw。本轮将原先零散的 inject / Feishu DM / host 路由判断重新收口成一条正式宿主集成链：AGM 发现新信 → 调 HappyClaw internal mailbox ingress → HappyClaw 解析 effective group / home session → 以“可触发 runtime 的宿主消息”送入主会话。对 OpenClaw 继续保留 external activator 路线；对 HappyClaw，则优先利用其主会话和 IM 路由能力完成更自然的消息触发。

**Tech Stack:** TypeScript, Node.js, AGM (`@t0u9h/agent-git-mail`), HappyClaw backend routes/services, Feishu IM routing, Git-backed mailbox.

---

## 0. 设计结论（拍板）

### 0.1 总体边界

**AGM 负责：**
- mailbox 协议与文件布局
- send / reply / archive
- recipient inbox 双写
- daemon 新信检测
- 激活去重 / checkpoint
- 宿主适配调用（host adapter / ingress client）

**HappyClaw 负责：**
- 把“新邮件到了”映射成主会话中的可处理输入
- 解析 IM group → effective group → sibling home / main session
- 复用已有消息入站链，保证 runtime trigger 成立
- 审计 source，不伪装普通用户手打消息

### 0.2 核心路线调整

**不建议把 HappyClaw 集成继续收在 `/internal/inject` 的通用注入语义里。**

应该升级成一个更明确的宿主能力：

- `POST /internal/agm/ingress`（推荐命名，可保留旧 inject 兼容层）

原因：
1. 当前目标不是通用注入平台，而是 **AGM mailbox 宿主接入点**
2. 需要明确 source / idempotency / routing / runtime trigger 语义
3. 需要和“普通 internal inject”区分开，避免后续权限和行为混淆

### 0.3 HappyClaw 要实现的能力

HappyClaw 对 AGM 的正式承诺应该是：

> 给我一条 mailbox event，我负责把它路由到正确主会话，并像真实外部输入一样足以唤起 agent。 

这里的重点不是“把文本塞进 transcript”，而是：

> **runtime-triggered main-session ingress**

### 0.4 OpenClaw vs HappyClaw 路线不强求完全一致

- **OpenClaw：** external activator 路更合理，因为宿主侧没有现成稳定 mailbox ingress 能力
- **HappyClaw：** 宿主自带 IM / group / home session 路由，更适合做内部 mailbox ingress

不要为了一致性牺牲正确性。

---

## 1. 要解决的真实问题

### 当前已知问题

1. AGM transport 已经可用，但 HappyClaw 还没有一个**正式宿主接入点**
2. 之前的 `/internal/inject` 路能跑通部分链路，但边界含混：
   - `is_home` 校验层级不对
   - 语义偏“通用注入”，不是 mailbox ingress
   - runtime trigger 语义没有产品化收口
3. 现在用户目标不是“系统里看得见提醒”，而是：
   - 新邮件进入后
   - Hex / HappyClaw 主会话能被稳定触发
   - 后续在正确 session 内处理 AGM 工作流

### 这轮设计要交付的结果

1. AGM 能把新邮件事件发给 HappyClaw
2. HappyClaw 能把事件送进正确主会话
3. 该事件足以触发 agent run
4. agent 收到后走 AGM mailbox workflow，而不是普通闲聊回复
5. 整套设计边界清楚，可持续维护

---

## 2. 文件结构与职责

### AGM 仓库（`/Users/wangguiping/workspace/agent-git-mail`）

#### 需要新增 / 修改的主要文件
- Modify: `packages/agm/src/app/run-daemon.ts`
  - 从“直接 external activator”扩成“宿主适配选择层”
- Create: `packages/agm/src/host-adapter/index.ts`
  - 统一宿主触发抽象（OpenClaw external activator / HappyClaw ingress）
- Create: `packages/agm/src/host-adapter/types.ts`
  - 宿主触发接口与结果结构
- Create: `packages/agm/src/host-adapter/happyclaw-ingress.ts`
  - 调 HappyClaw internal mailbox ingress
- Modify: `packages/agm/src/config/schema.ts`
  - 加入 HappyClaw ingress 配置
- Modify: `packages/agm/src/config/index.ts`
  - 读取宿主集成配置
- Modify: `packages/agm/test/...`
  - 加 HappyClaw ingress 相关测试

### HappyClaw 仓库（`/Users/wangguiping/happyclaw`）

#### 需要新增 / 修改的主要文件
- Modify: `src/routes/inject.ts` 或拆分为 `src/routes/agm-ingress.ts`
  - 推荐新增专用 AGM ingress route；inject 可做兼容层
- Create: `src/services/agm-ingress.ts`
  - AGM ingress 业务编排：解析 group、校验 target、幂等、入会话
- Modify: `src/...resolveEffectiveGroup...` 相关调用位置
  - 统一 effective group / home 校验逻辑
- Modify: `src/...message enqueue / store / broadcast...` 相关服务
  - 明确提供一个“可触发 runtime 的宿主输入入口”
- Create: `src/types/agm-ingress.ts`
  - request / response / sourceMeta 类型
- Modify: `src/routes/config.js` 对应 TS 源文件（若需可观测/绑定调试）
- Add tests: route/service/integration tests

### Knowledge / Docs
- Create: AGM 仓库内实施 plan / design note
- Create: HappyClaw 仓库内 host ingress design note
- Update: knowledge-vault 项目文档，记录最终边界

---

## 3. 目标接口设计

## 3.1 HappyClaw 宿主入口

### 推荐接口

```http
POST /internal/agm/ingress
```

### 请求体

```json
{
  "targetJid": "feishu:oc_xxx",
  "source": "agm",
  "sourceMeta": {
    "mailboxId": "hex",
    "messageId": "2026-04-03T09-00-00Z-mt-to-hex-abcd.md",
    "from": "mt",
    "subject": "Test",
    "reason": "new_mail"
  },
  "content": "[AGM ACTION REQUIRED]\nNew mail from mt\nNext step: agm read 2026-04-03T09-00-00Z-mt-to-hex-abcd.md"
}
```

### 响应体

```json
{
  "ok": true,
  "delivery": "ingressed",
  "effectiveGroupJid": "web:main",
  "targetJid": "feishu:oc_xxx",
  "messageId": "internal-..."
}
```

### 行为要求

1. `targetJid` 可以是 Feishu DM JID
2. HappyClaw 必须先做 `resolveEffectiveGroup(targetJid)`
3. 后续所有 `is_home` / sibling-home / route 判定都基于 **effective group**
4. 最终把内容送入对应主会话
5. 送入方式必须复用“足以触发 runtime”的宿主输入链，而不是仅写 transcript

---

## 3.2 AGM 侧宿主配置

### 推荐配置模型

```yaml
host_integration:
  kind: happyclaw
  happyclaw:
    base_url: http://127.0.0.1:3000/internal
    bearer_token_env: HAPPYCLAW_INTERNAL_SECRET
    target_jid: feishu:oc_xxx
```
```

### 与现有 activation 配置关系

建议这样处理：

- OpenClaw 路：继续叫 `activation`
- HappyClaw 路：新增 `host_integration`

不要强行把 HappyClaw ingress 也伪装成 activator。因为：
- activator 偏“外部唤醒器”
- HappyClaw ingress 偏“宿主原生 mailbox ingress”

两者职责不同。

---

## 4. 行为时序（正式闭环）

### HappyClaw 场景

1. `agm send/reply` 完成 recipient inbox 双写
2. recipient 机器上的 AGM daemon 轮询到新 inbox 文件
3. AGM daemon 检查 checkpoint
4. 未处理则构造 mailbox event
5. AGM 调 `POST /internal/agm/ingress`
6. HappyClaw：
   - resolve effective group
   - 找到 sibling home / main session
   - 校验 target 合法
   - 通过宿主真实输入入口把消息送进主会话
7. 主会话 agent 被触发
8. AGM skill / 系统消息引导 agent 执行：
   - `agm read <filename>`
   - 再决定 `reply/archive`
9. 成功返回后 AGM 写 checkpoint

### 失败语义

- ingress 失败 → 不写 checkpoint
- ingress 成功但后续 agent 不处理 → 仍写 checkpoint（因为宿主接收成功，mailbox trigger 已完成）

> 这里要明确：checkpoint 语义绑定“宿主触发成功”，不是“agent 已读完邮件”。

---

## 5. 关键设计判断

### 5.1 不能只修 `is_home` 一行就算完

虽然当前 blocker 表面上是：
- `is_home` 校验用了原始 group，而非 effective group

但如果只修这一行，仍然会留下 3 个结构问题：

1. endpoint 语义仍然是“通用 inject”，不是 AGM ingress
2. AGM 侧没有正式 host adapter 抽象
3. 成功/失败/幂等/审计语义还没为 mailbox 业务收口

所以这轮正确做法是：

> **把这次一行 bug 修复，放进一次正式 mailbox ingress 设计升级里完成。**

### 5.2 HappyClaw ingress 不应伪装成人类消息

即便要复用“可触发 runtime 的输入链”，也不应该把 source 伪装成用户本人手打消息。

必须保留：
- `sourceKind: injected` / `source: agm`
- `sourceMeta.messageId`
- 审计可追溯

否则后面会出现：
- 宿主日志不可区分
- 调试困难
- 安全边界模糊

### 5.3 触发 agent 的关键不是“长得像用户消息”，而是“走宿主正常入站链”

这个判断非常重要。

不要把方向收成：
- “我们要伪装用户消息”

正确收口应该是：
- **复用宿主那条真正会触发 runtime 的 ingress path**
- 但 source 仍可审计地标记为 `agm`

### 5.4 AGM 不应该知道 HappyClaw 内部 session 细节

AGM 只知道：
- targetJid（如 Feishu DM）
- host base URL
- bearer token

不要让 AGM 直接管理：
- sibling home 选择
- effective group 解析
- session 内部 id

这些都属于 HappyClaw 宿主职责。

---

## 6. 测试策略

## Chunk 1: HappyClaw ingress 设计与路由正确性

### Task 1: 修正 effective group / home 判定

**Files:**
- Modify: `happyclaw/src/routes/inject.ts` 或未来 `src/routes/agm-ingress.ts`
- Test: HappyClaw route tests

- [ ] **Step 1: 写失败测试：Feishu JID target 经过 resolveEffectiveGroup 后允许进入 home session**
- [ ] **Step 2: 跑测试确认当前因原始 group.is_home 判定失败**
- [ ] **Step 3: 改为基于 effective group 做 home 判定**
- [ ] **Step 4: 跑测试确认通过**
- [ ] **Step 5: commit**

### Task 2: 新增 AGM 专用 ingress route（或从 inject 拆语义）

**Files:**
- Create: `happyclaw/src/routes/agm-ingress.ts`
- Create: `happyclaw/src/services/agm-ingress.ts`
- Create: `happyclaw/src/types/agm-ingress.ts`
- Modify: route registration file
- Test: route/service tests

- [ ] **Step 1: 写失败测试：合法 AGM request 可返回 ok + effectiveGroupJid**
- [ ] **Step 2: 实现 request schema / auth / basic validation**
- [ ] **Step 3: 实现 sourceMeta + targetJid + content 校验**
- [ ] **Step 4: 跑测试确认通过**
- [ ] **Step 5: commit**

### Task 3: 接到“可触发 runtime”的宿主入站链

**Files:**
- Modify: `happyclaw/src/services/agm-ingress.ts`
- Modify: 宿主现有 message enqueue/store/broadcast 服务
- Test: integration test

- [ ] **Step 1: 写失败测试：AGM ingress 后能进入主会话且触发正常处理链**
- [ ] **Step 2: 复用已有真实入站 path，而不是只写 transcript**
- [ ] **Step 3: 返回 messageId / delivery metadata**
- [ ] **Step 4: 跑测试确认通过**
- [ ] **Step 5: commit**

## Chunk 2: AGM host adapter 设计与调用闭环

### Task 4: 为 AGM 新增 host adapter 抽象

**Files:**
- Create: `packages/agm/src/host-adapter/types.ts`
- Create: `packages/agm/src/host-adapter/index.ts`
- Test: unit tests

- [ ] **Step 1: 写失败测试：根据配置可选择 HappyClaw ingress / OpenClaw activation**
- [ ] **Step 2: 定义统一接口，例如 `deliverMailboxEvent(input)`**
- [ ] **Step 3: 接入现有 external activator 作为一种实现**
- [ ] **Step 4: 跑测试确认通过**
- [ ] **Step 5: commit**

### Task 5: HappyClaw ingress client

**Files:**
- Create: `packages/agm/src/host-adapter/happyclaw-ingress.ts`
- Modify: `packages/agm/src/config/schema.ts`
- Modify: `packages/agm/src/config/index.ts`
- Test: HTTP client tests

- [ ] **Step 1: 写失败测试：构造 HTTP request，成功/失败语义正确映射**
- [ ] **Step 2: 实现 bearer token / target_jid / content payload**
- [ ] **Step 3: 把 response 解析为 host adapter result**
- [ ] **Step 4: 跑测试确认通过**
- [ ] **Step 5: commit**

### Task 6: daemon 调用宿主适配层

**Files:**
- Modify: `packages/agm/src/app/run-daemon.ts`
- Test: daemon integration tests

- [ ] **Step 1: 写失败测试：新信 → HappyClaw ingress 成功 → 写 checkpoint**
- [ ] **Step 2: 写失败测试：HappyClaw ingress 失败 → 不写 checkpoint**
- [ ] **Step 3: 将当前 activator 触发逻辑改为 host adapter 调用**
- [ ] **Step 4: 保持 OpenClaw external activator 路仍兼容**
- [ ] **Step 5: 跑测试确认通过**
- [ ] **Step 6: commit**

## Chunk 3: 行为约束、文档与端到端验证

### Task 7: AGM workflow 引导收口

**Files:**
- Modify: AGM 通知文本生成位置
- Modify: skill / docs
- Test: integration / snapshot tests

- [ ] **Step 1: 确保 ingress 内容仍是 action-oriented mailbox message**
- [ ] **Step 2: 明确 `agm read <filename>` 为下一步**
- [ ] **Step 3: 跑相关测试**
- [ ] **Step 4: commit**

### Task 8: 端到端验证（HappyClaw）

**Files:**
- Add/Modify: integration test harness / scripts
- Docs: verification note

- [ ] **Step 1: 准备一条 Feishu JID → web:main 的真实或半真实测试场景**
- [ ] **Step 2: 验证新 mail → AGM daemon → HappyClaw ingress → 主会话可见**
- [ ] **Step 3: 验证 response 路由回 Feishu DM**
- [ ] **Step 4: 验证 ingress 失败时 checkpoint 不写**
- [ ] **Step 5: 记录关键日志 / transcript / result**
- [ ] **Step 6: commit**

### Task 9: 文档收口

**Files:**
- Create/Modify: AGM repo docs
- Create/Modify: HappyClaw repo docs
- Update: knowledge-vault notes

- [ ] **Step 1: 更新 AGM × HappyClaw 集成设计文档**
- [ ] **Step 2: 写清 OpenClaw 路 vs HappyClaw 路边界**
- [ ] **Step 3: 写清配置示例与失败语义**
- [ ] **Step 4: commit**

---

## 7. 验收标准

这轮改造只有满足以下条件，才算真正完成：

1. AGM 能以正式配置调用 HappyClaw mailbox ingress
2. HappyClaw 能接受 Feishu JID 作为 target，并正确 resolve 到主会话
3. `is_home` / sibling-home / route 判定基于 effective group，而非原始 group
4. ingress 成功后，主会话 agent 会被正常触发，不只是 transcript 可见
5. ingress 失败时 AGM 不写 checkpoint
6. response 路由仍能自然回到 Feishu DM
7. 文档里清楚区分：
   - OpenClaw external activator
   - HappyClaw host ingress
8. 不把 AGM 业务逻辑塞进 HappyClaw；不把 HappyClaw session 细节泄漏给 AGM

---

## 8. 额外风险与注意事项

### 风险 1：只修一行导致再次进入“局部可用但边界含混”状态
避免方式：
- 一行 bug 修复必须纳入正式 ingress 设计升级

### 风险 2：把 ingress 做成“伪装成用户消息”
避免方式：
- 审计字段必须保留 `source=agm`
- 只复用宿主真实触发链，不伪装身份

### 风险 3：AGM 和 HappyClaw 职责重新缠绕
避免方式：
- AGM 只负责 mail transport + host call
- HappyClaw 只负责 host ingress + routing + runtime trigger

### 风险 4：checkpoint 语义被误绑定到“agent 是否真的处理”
避免方式：
- checkpoint 只绑定“宿主 ingress 成功”
- 不绑定 agent 是否回复/归档

---

## 9. 推荐实施顺序

### 第一优先级（必须）
1. HappyClaw effective group / home 判定修正
2. HappyClaw AGM ingress route/service 收口
3. AGM host adapter 抽象
4. daemon 接 HappyClaw ingress

### 第二优先级（应做）
5. E2E 验证 Feishu JID → web:main → response 回 DM
6. 文档和配置示例收口

### 第三优先级（可后置）
7. 兼容层：旧 `/internal/inject` 到新 `/internal/agm/ingress` 的平滑过渡
8. doctor/self-check 补 HappyClaw host integration 检查项

---

## 10. 最终推荐结论

这轮不应该把问题理解成：

> “修 inject.ts 那一行就完了。”

正确理解应该是：

> **借这次 `is_home` 判定 bug，把 HappyClaw 对 AGM 的宿主能力正式收成 mailbox ingress。**

这样做完后，体系会变成：

- **AGM**：独立 mailbox transport + daemon + host adapter
- **OpenClaw**：external activator 宿主
- **HappyClaw**：host-native mailbox ingress 宿主

这是能长期维护的边界。
