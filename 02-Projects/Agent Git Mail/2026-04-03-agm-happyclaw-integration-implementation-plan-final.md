# AGM × HappyClaw Integration Implementation Plan (Final)

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 基于正式 mailbox ingress 设计，把 AGM 稳定接入 HappyClaw，让 HappyClaw 主会话能够消费 AGM 新邮件信号并触发 agent 处理，同时保持 AGM / HappyClaw / OpenClaw 的职责边界长期清晰。

**Architecture:** AGM 继续负责 mailbox transport、daemon、checkpoint 与宿主调用；HappyClaw 提供 AGM 专用 mailbox ingress，负责 Feishu JID → effective group → sibling home / main session 路由，并通过真实宿主入站链触发 runtime；OpenClaw 继续保留 external activator 路，不要求与 HappyClaw 强行统一实现。

**Tech Stack:** TypeScript, Node.js, AGM (`@t0u9h/agent-git-mail`), HappyClaw backend routes/services, Feishu IM routing, Git-backed mailbox, internal HTTP ingress.

---

## 一、最终拍板

## 1.1 总体方向

这轮不再把问题理解成：

- “修 `inject.ts` 的一行 `is_home` 判定就完了”

而是正式收口成：

- **AGM = mailbox transport + daemon + host adapter**
- **HappyClaw = mailbox ingress 宿主**
- **OpenClaw = external activator 宿主**

一句话：

> **把当前一行 blocker 纳入正式宿主接入设计升级，而不是做局部补丁。**

## 1.2 必须坚持的 4 条硬约束

### 约束 1：HappyClaw 对 AGM 提供的是 mailbox ingress，不是通用 inject
- 不继续把 AGM 挂死在含混的 `/internal/inject` 语义上
- 推荐新增专用入口：`/internal/agm/ingress`

### 约束 2：所有 home/main 判定都基于 `resolveEffectiveGroup`
- 不能继续混用原始 group 与 effective group
- `is_home`、sibling-home、主会话合法性都基于 effective group

### 约束 3：checkpoint 只绑定宿主 ingress 成功
- ingress success → 写 checkpoint
- ingress failure → 不写 checkpoint
- 不把 checkpoint 绑定到 agent 是否读信/回复/归档

### 约束 4：runtime trigger 比 transcript delivery 更重要
- 不接受“消息进 transcript 但 agent 不起”的伪完成
- HappyClaw ingress 的成功标准必须包含：走到真正会触发 runtime 的宿主入站链

---

## 二、仓库边界

## 2.1 AGM（`agent-git-mail`）职责

### 负责
- mailbox 协议
- send / reply / archive
- recipient inbox 双写
- daemon 新信检测
- checkpoint / 去重
- 通过宿主适配层调用宿主 ingress / activator

### 不负责
- HappyClaw 内部 group/session 拓扑
- `resolveEffectiveGroup`
- sibling home 选择
- HappyClaw 的主会话内部路由细节

## 2.2 HappyClaw 职责

### 负责
- 提供 AGM 专用 ingress endpoint
- `targetJid` → effective group → sibling home / main session 解析
- 复用真实宿主入站链触发 runtime
- source / sourceMeta 审计
- 路由结果与 delivery metadata 返回

### 不负责
- mailbox transport 本体
- send / reply / archive 协议
- checkpoint 真相判断
- mailbox 业务状态机

## 2.3 OpenClaw 职责

保持现有路线：
- 继续走 external activator
- 不要求和 HappyClaw 强行统一成同一个内部抽象实现

> 统一顶层语义，不统一宿主细节。

---

## 三、目标接口设计

## 3.1 HappyClaw：新增正式 endpoint

### 推荐接口

```http
POST /internal/agm/ingress
```

### 为什么不用继续扩 `/internal/inject`
1. AGM 是单一明确 source，值得拥有专用入口
2. mailbox ingress 需要自己明确的：
   - target 约束
   - source 语义
   - response 结构
   - 审计与幂等
3. 通用 inject 后续会让权限、语义、排障都变混

### 兼容建议
- 短期可保留 `/internal/inject`
- AGM 新接入直接走 `/internal/agm/ingress`
- 旧 inject 路逐步降为兼容层，不再作为 AGM 正式接入面

## 3.2 Request / Response 模型

### Request

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

### Response

```json
{
  "ok": true,
  "delivery": "ingressed",
  "targetJid": "feishu:oc_d032da393cc3bdabec385602be187652",
  "effectiveGroupJid": "web:main",
  "messageId": "internal-123"
}
```

## 3.3 HappyClaw 校验顺序（必须固定）

1. 校验 internal bearer token / secret
2. 解析 `targetJid`
3. `resolveEffectiveGroup(targetJid)`
4. 基于 **effective group** 做：
   - `is_home`
   - sibling-home
   - main session 合法性判断
5. 构造 `source=agm`、`sourceMeta` 完整的宿主输入
6. 送入真实 runtime-triggering ingress path
7. 返回 delivery metadata

### 关键实现风险（Hex 提出的 blocker）
需要确保：
- `resolveEffectiveGroup` 能从当前定义位置正确导出到需要的上下文（如 `web-context.ts` 等实际调用层）

这不是小细节，是 HappyClaw Phase 1 的真实 blocker。

---

## 四、AGM 配置与抽象设计

## 4.1 新增 host adapter 抽象

推荐新增统一抽象：

```ts
interface HostMailboxIngress {
  name: string;
  deliver(input: HostMailboxIngressInput): Promise<HostMailboxIngressResult>;
}
```

### 两类实现
- OpenClaw external activator（现有）
- HappyClaw ingress（新增）

### 为什么必须抽象
因为 daemon 的职责已经不是“固定发一条外部消息”，而是：
- 根据宿主类型选择不同触发路径
- 但顶层成功/失败/checkpoint 语义应统一

## 4.2 AGM 配置扩展

推荐新增：

```yaml
host_integration:
  kind: happyclaw
  happyclaw:
    base_url: http://127.0.0.1:3000/internal
    bearer_token_env: HAPPYCLAW_INTERNAL_SECRET
    target_jid: feishu:oc_xxx
```

### 设计判断
- `activation` 继续给 OpenClaw external activator 使用
- `host_integration` 专门给 HappyClaw ingress 使用

不要把 HappyClaw ingress 塞进 `activation`。

### 关键风险（Hex 提出的第二个风险）
必须提前统一：
- HappyClaw internal secret 的环境变量名
- AGM daemon 读取该 secret 的方式
- launchd / nohup / shell 启动环境是否一致

否则联调阶段会出现“接口对了但 token 不一致”的伪故障。

---

## 五、代码改动清单

## 5.1 HappyClaw 仓库

### 新增文件
- Create: `src/routes/agm-ingress.ts`
- Create: `src/services/agm-ingress.ts`
- Create: `src/types/agm-ingress.ts`

### 修改文件
- Modify: route registration file（接入新 route）
- Modify: 与 `resolveEffectiveGroup` / `web-context` / group routing 相关文件
- Modify: 现有 message ingress / enqueue / store / broadcast 相关服务

### 职责说明

#### `src/routes/agm-ingress.ts`
- internal auth
- request schema validation
- 调 service
- 返回结构化 response

#### `src/services/agm-ingress.ts`
- `resolveEffectiveGroup(targetJid)`
- effective group 基础上的 home/main 判定
- 构造 `source=agm` 的宿主输入
- 送入真实 runtime-triggering ingress path
- 返回 delivery metadata

#### `src/types/agm-ingress.ts`
- request / response / sourceMeta 类型
- route 与 service 共用模型

## 5.2 AGM 仓库

### 新增文件
- Create: `packages/agm/src/host-adapter/types.ts`
- Create: `packages/agm/src/host-adapter/index.ts`
- Create: `packages/agm/src/host-adapter/happyclaw-ingress.ts`

### 修改文件
- Modify: `packages/agm/src/app/run-daemon.ts`
- Modify: `packages/agm/src/config/schema.ts`
- Modify: `packages/agm/src/config/index.ts`
- Modify/Add: 相关测试文件

### 职责说明

#### `host-adapter/types.ts`
- 定义 ingress input/result 类型
- 统一成功/失败语义

#### `host-adapter/index.ts`
- 根据 config 选择 HappyClaw ingress 或 OpenClaw activator

#### `host-adapter/happyclaw-ingress.ts`
- 发 internal HTTP request
- bearer token / target_jid / content payload
- 映射 HappyClaw response 与错误语义

#### `run-daemon.ts`
- 从“直接 activator”升级成“调用 host adapter”
- ingress success → 写 checkpoint
- ingress failure → 不写 checkpoint
- 保持 OpenClaw external activator 路兼容

---

## 六、分阶段实施计划（最终版）

## Phase 1：HappyClaw 路由 blocker 修复 + ingress 骨架

**目标：** 先把 Feishu JID → effective group → main session 路由打通。

### 任务
- [ ] 修正当前 `is_home` 判定：基于 effective group，而不是原始 group
- [ ] 把 `resolveEffectiveGroup` 复用边界整理好，能被 ingress 路由层调用
- [ ] 新增 `/internal/agm/ingress` route 骨架
- [ ] 新增 request schema / auth / response model

### 预期改动
- 3 个新文件 + 2 处小改（与 Hex 版拆法一致）

### 验收
- Feishu JID target 不再被错误挡在 home 校验前
- route 可返回基本成功结果与 `effectiveGroupJid`

## Phase 2：HappyClaw 接 runtime-triggering ingress path

**目标：** 不只是可路由，而是真正触发宿主 runtime。

### 任务
- [ ] 在 `agm-ingress service` 中接入现有真实宿主入站链
- [ ] 保留 `source=agm` / `sourceMeta` 审计
- [ ] 返回 messageId / delivery metadata

### 验收
- 不接受“只进 transcript”
- 必须有证据表明进入了真正会触发 runtime 的宿主链

## Phase 3：AGM host adapter 抽象与 HappyClaw 实现

**目标：** 让 AGM 能正式选择 HappyClaw ingress 作为宿主触发方式。

### 任务
- [ ] 新增 host adapter 抽象
- [ ] 新增 HappyClaw ingress client
- [ ] 新增 `host_integration` 配置
- [ ] 保持 OpenClaw external activator 兼容

### 验收
- config 可选择 HappyClaw ingress
- 调用成功/失败语义稳定映射

## Phase 4：daemon 接 host adapter + checkpoint 收口

**目标：** AGM daemon 完整接上新宿主能力。

### 任务
- [ ] daemon 改为通过 host adapter 触发宿主
- [ ] ingress success → 写 checkpoint
- [ ] ingress failure → 不写 checkpoint
- [ ] 事件日志补齐（必要时区分 host ingress 事件）

### 验收
- checkpoint 语义完全与 ingress success 对齐

## Phase 5：E2E 联调与文档收口

**目标：** 真正把 HappyClaw 线路跑通并形成可维护文档。

### 任务
- [ ] Feishu JID → web:main → runtime trigger 验证
- [ ] response 路由回 Feishu DM 验证
- [ ] ingress failure 不写 checkpoint 验证
- [ ] 更新文档，明确 OpenClaw 路 vs HappyClaw 路边界

### 验收
- 有完整证据链：request / route / session / response / checkpoint

---

## 七、测试计划

## 7.1 HappyClaw 测试

### 路由测试
- [ ] 合法 token + 合法 AGM request → `200 ok`
- [ ] 非法 token → `401/403`
- [ ] targetJid 不存在 → 合理错误
- [ ] Feishu JID target 经 `resolveEffectiveGroup` 后命中 `web:main`

### 行为测试
- [ ] 原始 group.is_home 路径会失败（先写旧行为失败测试）
- [ ] effective group.is_home 路径通过
- [ ] ingress 成功后进入主会话链
- [ ] `source=agm` / `sourceMeta` 被保留

### 集成测试
- [ ] AGM ingress → message stored / broadcast / enqueue 完整链成立
- [ ] response 路由回 Feishu DM 正确

## 7.2 AGM 测试

### 单测
- [ ] config 解析 HappyClaw ingress 配置
- [ ] host adapter 根据 config 选择正确实现
- [ ] HappyClaw ingress client 成功/失败语义映射

### daemon 测试
- [ ] ingress success → checkpoint written
- [ ] ingress failure → checkpoint not written
- [ ] OpenClaw activator 路线不回归

### E2E
- [ ] sender 双写 recipient inbox
- [ ] daemon 检测新信
- [ ] HappyClaw ingress 成功
- [ ] checkpoint 写入

---

## 八、风险清单（最终版）

### 风险 1：只修一行 `is_home`，导致体系继续含混
**应对：** 必须同步引入正式 `/internal/agm/ingress` 设计。

### 风险 2：`resolveEffectiveGroup` 复用边界不清
**应对：** 把它导出到正确上下文，作为 Phase 1 blocker 优先解决。

### 风险 3：secret 环境变量不一致
**应对：** 在计划早期统一：变量名、读取方式、运行环境注入方式。

### 风险 4：只写 transcript，不触发 runtime
**应对：** success 标准必须要求 runtime trigger，而不是只看 transcript 文本。

### 风险 5：HappyClaw 细节泄露进 AGM
**应对：** AGM config 只保留 `target_jid` 与 host endpoint，不暴露内部 session 细节。

### 风险 6：把 source 伪装成用户消息
**应对：** 保留 `source=agm` 与审计字段，只复用真实宿主入站链，不伪装身份。

---

## 九、推荐执行顺序（最终）

如果只允许给一个执行顺序，采用如下版本：

1. **HappyClaw 先修 effective group / home blocker**
2. **HappyClaw 新增 `/internal/agm/ingress`**
3. **HappyClaw 接 runtime-triggering ingress path**
4. **AGM 新增 host adapter 与 HappyClaw client**
5. **daemon 改接 host adapter，收口 checkpoint**
6. **做一条完整 E2E，确认 response 回 DM**
7. **最后更新文档与兼容层**

---

## 十、最终结论

这份最终版融合了两部分优点：

- **Hex 版**：阶段拆分干净，工程执行感强
- **MT 版**：边界、语义、风险收口更硬

最终 baseline 应以这份为准：

> **采用 Hex 的实施分期，吸收 MT 这边 4 条硬约束与风险收口，作为 AGM × HappyClaw integration 的正式 implementation baseline。**
