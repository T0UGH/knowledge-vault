# AGM × HappyClaw Integration Implementation Plan (MT)

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 基于新的 mailbox ingress 设计，把 AGM 正式接入 HappyClaw，让 HappyClaw 主会话能稳定消费 AGM 新邮件信号，并保持 AGM/OpenClaw/HappyClaw 三者边界清晰。

**Architecture:** AGM 保持独立 mailbox transport + daemon，不把 mailbox 业务灌进 HappyClaw。HappyClaw 提供 AGM 专用 ingress 宿主能力，负责 Feishu JID 到 effective group / sibling home / main session 的解析，并通过真实宿主入站链触发 runtime。OpenClaw 继续保留 external activator 路，不与 HappyClaw 强行统一成一个抽象。

**Tech Stack:** TypeScript, Node.js, AGM (`@t0u9h/agent-git-mail`), HappyClaw backend routes/services, Feishu IM routing, Git-backed mailbox.

---

## 一、实施原则

### 1. 不把这轮问题收成“一行 bug”
当前表面 blocker 确实是：
- `inject.ts` 的 `is_home` 校验用的是原始 group，而不是 `resolveEffectiveGroup` 后的结果

但如果只修这一行，会留下三个长期问题：
1. HappyClaw 仍然只有一个含混的通用 inject 入口
2. AGM 侧没有正式的 HappyClaw 宿主适配层
3. mailbox ingress 的成功/失败/审计/幂等语义没有定稿

所以这轮要做的是：
> **借这次 bug，把 HappyClaw 对 AGM 的宿主接入能力正式收口。**

### 2. 不伪装成人类消息，但必须走真实触发链
目标不是“造假成贵平亲手发的一条话”，而是：
- source 仍然可审计地标记为 `agm`
- 但走到 HappyClaw 那条真正会触发 runtime 的宿主输入链

一句话：
> **保留 source 审计，复用 runtime trigger。**

### 3. AGM 不知道 HappyClaw 的内部 session 细节
AGM 只需要知道：
- HappyClaw internal API base URL
- bearer token / secret
- target JID（如 Feishu DM）
- mail metadata / content

AGM 不负责：
- effective group 解析
- sibling home 选择
- `is_home` 判定
- main session 选择

这些必须全部留在 HappyClaw 宿主侧。

---

## 二、仓库边界

## 2.1 `agent-git-mail`

### 该仓库要做的事情
1. 新增 HappyClaw 宿主适配能力
2. daemon 从“只会 external activator”升级成“按宿主集成配置选择触发方式”
3. 明确 checkpoint 语义：
   - 宿主 ingress 成功 → 写 checkpoint
   - 宿主 ingress 失败 → 不写 checkpoint
4. 保持 OpenClaw external activator 路兼容

### 该仓库不要做的事情
1. 不解析 HappyClaw group/session 拓扑
2. 不直接操纵 HappyClaw 内部函数
3. 不知道 `resolveEffectiveGroup` 的宿主细节

## 2.2 `happyclaw`

### 该仓库要做的事情
1. 新增 AGM 专用 ingress route/service
2. 基于 `resolveEffectiveGroup` 做 target 解析
3. 基于 effective group 做 home/main session 合法性判定
4. 复用现有“真正会触发 runtime”的宿主入站链
5. 保留 `source=agm` 与 `sourceMeta` 审计信息

### 该仓库不要做的事情
1. 不实现 mailbox transport 本体
2. 不负责 send/reply/archive 协议
3. 不负责 checkpoint 真相判断

---

## 三、推荐方案拍板

## 3.1 HappyClaw：新增 `/internal/agm/ingress`

**推荐新增专用接口，而不是继续把 AGM 绑死在通用 `/internal/inject` 上。**

### 原因
1. AGM 是明确的单一 source，值得拥有专用宿主入口
2. `inject` 语义太泛，后续会让权限、行为、审计都变混
3. mailbox ingress 需要有自己明确的：
   - target 约束
   - source 约束
   - response 结构
   - 幂等与失败语义

### 兼容建议
- 短期可以保留 `/internal/inject` 不删
- 但 AGM 新接入应直接走 `/internal/agm/ingress`
- 旧 inject 如仍被内部使用，可逐步收缩到兼容层

## 3.2 AGM：新增 host adapter 抽象

推荐新增统一抽象，例如：

```ts
interface HostMailboxIngress {
  name: string;
  deliver(input: HostMailboxIngressInput): Promise<HostMailboxIngressResult>;
}
```

然后有两类实现：
- OpenClaw external activator（现有路线）
- HappyClaw AGM ingress（新路线）

### 为什么要抽象
因为 daemon 当前逻辑已经不再只是“发一条外部消息”，而是：
- 根据宿主类型，选择不同 trigger 方式
- 但成功/失败/checkpoint 的顶层语义应该统一

---

## 四、接口与数据模型

## 4.1 HappyClaw ingress request

### Endpoint
```http
POST /internal/agm/ingress
```

### Request body
```json
{
  "targetJid": "feishu:oc_d032da393cc3bdabec385602be187652",
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

### Response body
```json
{
  "ok": true,
  "delivery": "ingressed",
  "targetJid": "feishu:oc_d032da393cc3bdabec385602be187652",
  "effectiveGroupJid": "web:main",
  "messageId": "internal-123"
}
```

## 4.2 HappyClaw 校验顺序

HappyClaw 侧推荐固定成下面顺序：

1. 校验 bearer token / internal secret
2. 解析 `targetJid`
3. `resolveEffectiveGroup(targetJid)`
4. 基于 **effective group** 做 `is_home` / sibling-home / main session 路由判断
5. 构造 source 为 `agm` 的宿主输入
6. 走真正触发 runtime 的入站链
7. 返回 delivery metadata

> **重点：所有 home 合法性判断都必须基于 effective group，而不是原始 target group。**

## 4.3 AGM 配置扩展

推荐在 AGM config 中新增：

```yaml
host_integration:
  kind: happyclaw
  happyclaw:
    base_url: http://127.0.0.1:3000/internal
    bearer_token_env: HAPPYCLAW_INTERNAL_SECRET
    target_jid: feishu:oc_xxx
```

### 设计判断
- `activation` 字段继续给 OpenClaw external activator 使用
- `host_integration` 专门给宿主 ingress 使用

不要把 HappyClaw ingress 硬塞进 `activation`，否则概念会混：
- activation = 外部唤醒器
- host_integration = 宿主原生 mailbox ingress

---

## 五、代码改动清单

## 5.1 AGM 仓库改动

### Files
- Create: `packages/agm/src/host-adapter/types.ts`
- Create: `packages/agm/src/host-adapter/index.ts`
- Create: `packages/agm/src/host-adapter/happyclaw-ingress.ts`
- Modify: `packages/agm/src/app/run-daemon.ts`
- Modify: `packages/agm/src/config/schema.ts`
- Modify: `packages/agm/src/config/index.ts`
- Test: `packages/agm/test/...`

### 具体职责

#### `host-adapter/types.ts`
定义：
- ingress input/result 类型
- 统一成功/失败语义

#### `host-adapter/index.ts`
负责：
- 根据 config 选择 HappyClaw ingress 或 OpenClaw activator

#### `host-adapter/happyclaw-ingress.ts`
负责：
- 发 HTTP request
- 映射返回结果
- 统一错误语义

#### `run-daemon.ts`
改造点：
- 把当前 activator 逻辑提升成 host adapter 调用
- 成功后写 checkpoint
- 失败时不写 checkpoint
- 事件日志中区分 `activation_sent` vs `host_ingress_sent`（建议后者新增）

#### `config/schema.ts`
新增：
- `host_integration.kind`
- `host_integration.happyclaw.base_url`
- `host_integration.happyclaw.bearer_token_env`
- `host_integration.happyclaw.target_jid`

## 5.2 HappyClaw 仓库改动

### Files
- Create: `src/routes/agm-ingress.ts`
- Create: `src/services/agm-ingress.ts`
- Create: `src/types/agm-ingress.ts`
- Modify: route registration file
- Modify: 现有 message ingress / enqueue / broadcast / session routing 服务
- Test: route/service/integration tests

### 具体职责

#### `src/routes/agm-ingress.ts`
负责：
- internal auth
- request schema validation
- 调 service
- 返回结构化 response

#### `src/services/agm-ingress.ts`
负责：
- `resolveEffectiveGroup(targetJid)`
- 基于 effective group 做 home/main session 合法性判定
- 构造 `source=agm` 的宿主消息输入
- 送入真实 runtime-triggering ingress path
- 返回 delivery metadata

#### `src/types/agm-ingress.ts`
负责：
- request/response/sourceMeta 类型
- 保证 route/service 共用模型

#### 现有 ingress / enqueue 服务改动
目标是抽一个可复用入口，例如：
- `ingressSystemLikeMessage(...)`
- 或 `ingressRuntimeTriggeringMessage(...)`

但注意：
- 名字可以按 HappyClaw 当前代码风格定
- 关键不是命名，而是**必须进入真实会触发 runtime 的宿主入站链**

---

## 六、分阶段实施计划

## Phase 1：HappyClaw 路由与 blocker 修复

**目标：** 先让 Feishu JID target 的 effective group / home 判定正确。

### 任务
- 修 route/service 中的 `is_home` 判定层级
- 写测试覆盖：
  - Feishu JID target
  - resolveEffectiveGroup 后命中 `web:main`
  - 原始 group 非 home，但 effective group 是 home 的情况

### 完成标准
- 不再因为原始 group.is_home 阻断 Feishu DM → main session 路

## Phase 2：HappyClaw AGM ingress 正式化

**目标：** 把临时 inject 路线收成正式 `/internal/agm/ingress`。

### 任务
- 新增 route/service/types
- 实现 request schema / auth / response model
- 保留 source=agm / sourceMeta 审计
- 接入真实 runtime-triggering ingress path

### 完成标准
- 单测/路由测能证明 AGM request 可进入正确主会话

## Phase 3：AGM host adapter 接入

**目标：** 让 AGM daemon 能正式调用 HappyClaw ingress。

### 任务
- 加 host adapter 抽象
- 加 HappyClaw ingress client
- 改 daemon 触发逻辑
- 保持 OpenClaw external activator 兼容

### 完成标准
- 新邮件 → AGM daemon → HappyClaw ingress → 成功写 checkpoint
- ingress 失败 → 不写 checkpoint

## Phase 4：端到端验证

**目标：** 跑通 HappyClaw 真链路。

### 任务
- 以真实或半真实 Feishu JID 场景验证
- 验证 response 路由回 Feishu DM
- 验证日志、session transcript、checkpoint 三边一致

### 完成标准
- 可给出一条完整验证证据链

## Phase 5：文档与兼容层收口

**目标：** 防止后续又回到“只有口头结论”。

### 任务
- 更新 AGM / HappyClaw docs
- 写清 OpenClaw 路 vs HappyClaw 路
- 视情况保留旧 inject 兼容层并标注 deprecated

---

## 七、测试计划

## 7.1 HappyClaw 测试

### 路由测试
1. 合法 bearer token + 合法 AGM request → `200 ok`
2. 非法 token → `401/403`
3. targetJid 不存在 → 合理错误
4. Feishu JID target 经 `resolveEffectiveGroup` 后允许进入 `web:main`

### 行为测试
1. 使用原始 group.is_home 会失败（旧行为测试，可先写再修）
2. 使用 effective group.is_home 后通过
3. ingress 成功后能进入主会话消息链
4. source / sourceMeta 审计字段保留

### 集成测试
1. AGM ingress request → message stored/broadcast/enqueue 完整链成立
2. response 路由回 Feishu DM 正确

## 7.2 AGM 测试

### 单测
1. config 解析 HappyClaw ingress 配置
2. host adapter 根据 config 选择正确实现
3. HappyClaw ingress client 成功/失败语义映射

### daemon 测试
1. ingress success → checkpoint written
2. ingress failure → checkpoint not written
3. OpenClaw 路线不回归

### E2E
1. sender 双写 recipient inbox
2. daemon 检测新信
3. HappyClaw ingress 成功
4. checkpoint 写入

---

## 八、风险与注意事项

### 风险 1：把 `/internal/agm/ingress` 做成通用注入平台
避免方式：
- endpoint 只服务 AGM
- source 固定 `agm`
- 不要把它抽象成“万能内部发消息”

### 风险 2：runtime trigger 仍未真正打通
避免方式：
- 不接受“只进 transcript 就算成功”
- 必须验证 agent run 被触发

### 风险 3：checkpoint 语义定义错误
建议固定：
- checkpoint = 宿主 ingress 成功
- 不是 agent 读信成功
- 不是 reply/archive 成功

### 风险 4：HappyClaw 细节泄露进 AGM
避免方式：
- AGM config 只存 `target_jid`
- 不存 `effectiveGroupJid` / `main session id`

### 风险 5：OpenClaw / HappyClaw 强行统一
避免方式：
- 只统一顶层语义，不统一宿主细节
- OpenClaw 继续 activator
- HappyClaw 走 ingress

---

## 九、我建议的最终拍板

如果只允许我给一个执行建议，我会这么定：

1. **HappyClaw 先修 effective group / home blocker**
2. **直接新增 `/internal/agm/ingress`**，不要继续扩通用 inject
3. **AGM 新增 host adapter 抽象**，把 HappyClaw ingress 作为正式宿主实现
4. **先完成 HappyClaw 一条 E2E**，再考虑 doctor / self-check / 兼容层

一句话：
> **先把 HappyClaw 收成正式 mailbox ingress 宿主，再让 AGM 正式接它。**

---

## 十、验收标准

只有满足下面这些，才算真正完成：

- [ ] HappyClaw 可接受 AGM ingress request
- [ ] Feishu JID target 能 resolve 到正确主会话
- [ ] `is_home` 等合法性判断基于 effective group
- [ ] ingress 走到真正能触发 runtime 的宿主入站链
- [ ] AGM daemon 成功/失败与 checkpoint 语义正确
- [ ] OpenClaw external activator 路不回归
- [ ] response 路由能自然回 Feishu DM
- [ ] 文档写清宿主边界与配置方式
