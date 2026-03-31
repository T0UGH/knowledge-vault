---
title: AGM review：配置模型应回到 v1（self + contacts）并进入下一轮修订
date: 2026-03-31
tags:
  - agent-git-mail
  - review
  - config
  - mvp
status: in-progress
summary: 本轮 review 结论：AGM 当前不应宣称达到 MVP。核心问题不是单个 bug，而是配置模型从目标中的 self + contacts 漂移成了 self-only，导致 send/reply 主链路与文档、测试、bootstrap 语义失配。下一轮应先把配置模型、实现、测试、文档一起收口。
---

# AGM review：配置模型应回到 v1（self + contacts）并进入下一轮修订

## 结论

本轮提交把项目往前推了一步，但**现在还不建议宣称 AGM 已达到 MVP**。

最核心的问题不是代码风格，也不是小 bug，而是：

> 配置模型从原本更合理的 **self + contacts**，漂移成了 **self-only**。

这会直接导致：
- bootstrap 看起来成立
- 但真正 mail 系统最关键的 `send / reply` 寻址语义被打断
- README / 文档 / 测试 / 实现不再是同一个模型

## 昨天在 Obsidian 里实际上出现过两层方案

### 方案一：真正想要的产品语义版
来源：`2026-03-30-agm-mvp-supplement-thinking.md`

```yaml
self:
  agent_id: mt
  mailbox_repo_path: /path/to/my-mailbox

contacts:
  hex:
    repo_path: /path/to/hex-mailbox

notifications:
  default_target: main
  forced_session_key: null
```

这个方案的核心价值是：
- 能表达“我是谁”
- 能表达“我自己的 mailbox 在哪”
- 能表达“我要给谁发信”
- `contacts` 明确承担通讯录 / address-book 语义

### 方案二：bootstrap 第一版的阶段性妥协版
来源：`2026-03-30-agm-bootstrap-implementation-direction.md`

```yaml
self:
  id: mt
  repo_path: /path/to/my-mailbox

notifications:
  default_target: main
  forced_session_key: null

runtime:
  poll_interval_seconds: 30
```

这个方案不是完全错误，但它的边界应该非常明确：

> 它只适合 bootstrap 第一版“先至少能收通知”的最小初始化目标，
> 不能替代 AGM 的完整运行时配置模型。

## 当前 review 判断

**应该回到方案一。**

也就是：
- `self-only` 可以保留为 bootstrap 最小初始化输出的一种阶段形态
- 但 AGM 的正式运行时配置应回到 **self + contacts**
- 否则 mail 系统没有完成“身份 + 通讯录”这两个最基本的产品语义

## 本轮实际发现的问题

## P0-1：配置模型走偏

当前实现把 v1 理解成了只剩：

```yaml
self:
  id: mt
  repo_path: ...
```

这会导致完整系统缺少通讯录语义。

而 AGM 不是单机通知器，它是 mail 系统。
mail 系统至少要同时解决：
1. 我是谁
2. 我的 mailbox 在哪
3. 我要怎么给别人发信

缺第 3 条，模型就不完整。

## P0-2：当前实现已经把 send/reply 主链路打断

本地手工验证结果：

在当前 `self-only` 配置下执行：

```bash
agm send --from mt --to hex ...
```

会报：

```bash
Unknown agent: hex
```

这说明：
- 当前配置模型不能支撑最基本的跨 agent 寻址
- bootstrap 初始化产物与 mail 主链路不一致
- 这不是边角问题，而是主链路断裂

`reply` 也存在同类风险，因为 reply 需要根据原信 `from` 去反查对方 repo。

## P0-3：测试没有跟着新模型升级

当前测试大多还覆盖旧的 `agents:` 结构。
因此：
- CI 是绿的
- 但没有捕获这次 `self-only` 带来的真实回归

这类问题危险在于：

> 测试通过给了错误安全感，但真正产品语义已经漂移。

## P1-1：bootstrap 的 `--config-path` 有真实 bug

手工验证发现：
- 如果传自定义 `--config-path`
- 且目标父目录不存在
- 当前实现会直接 `ENOENT`

原因是实现里创建的是默认 config dir，而不是 `targetConfigPath` 的父目录。

所以当前不能宣称“已支持自定义 config path”。

## P1-2：system dependency 检查与描述不一致

commit 描述提到：
- `checkSystemDeps` 接受 `openclaw` / `openclaw-gateway`

但源码里仍是硬编码检查：

```ts
['git', 'node', 'npm', 'openclaw']
```

这至少说明：
- 实现、描述或构建结果之间存在不一致
- 需要统一，不然 review 会失真

## P1-3：README / 文档 / 实现三套叙事未收口

当前至少同时存在：
- README 里的 `agents:`
- bootstrap 文档里的 `self-only`
- 更合理的目标模型 `self + contacts`

这会带来几个直接后果：
- 用户不知道该配哪种
- 实现者容易把阶段性方案误当最终方案
- review 时容易反复争论“到底哪版才是对的”

## 建议的下一轮修订方向

### 1. 正式把配置模型收口到 self + contacts

建议下一轮明确统一成：

```yaml
self:
  id: mt
  repo_path: /path/to/my-mailbox

contacts:
  hex:
    repo_path: /path/to/hex-mailbox

notifications:
  default_target: main
  forced_session_key: null

runtime:
  poll_interval_seconds: 30
```

是否用 `agent_id/mailbox_repo_path` 还是 `id/repo_path`，可以再统一；
但语义上至少要有两层：
- `self`
- `contacts`

### 2. send/reply/daemon/plugin 全部跟上新模型

需要一起检查：
- `send`
- `reply`
- `read/list/archive`
- `daemon`
- `openclaw-plugin`

不要只改 schema，不改主链路。

### 3. 补对应测试

至少补：
- `self + contacts` 下 send 正常
- `self + contacts` 下 reply 正常
- 缺 contacts 时错误清晰
- `bootstrap` 输出与运行时模型关系明确
- `--config-path` 覆盖自定义父目录不存在场景

### 4. 最后统一 README / docs / bootstrap 文案

顺序建议是：
1. 先定正式配置模型
2. 再修实现
3. 再补测试
4. 最后统一 README / docs

不要反过来先写文档再赌实现能补上。

## 当前阶段判断

这轮不是“做得不行”，而是**已经推进到需要正式收口产品模型的时候了**。

一句话总结：

> AGM 的正式运行时配置应回到 **self + contacts**；
> `self-only` 只适合作为 bootstrap 的阶段性最小初始化格式，不能替代完整配置模型。

## 下一步

- 已决定：把本轮 review 结论写信给 Hex
- 要求其按 `self + contacts` 方向再改再测一轮
- 下一轮验收重点：配置模型、主链路、测试、文档四者是否重新一致
