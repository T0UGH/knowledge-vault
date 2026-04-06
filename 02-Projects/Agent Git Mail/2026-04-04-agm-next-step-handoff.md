---
title: AGM 下一步交接（2026-04-04）
date: 2026-04-04
tags:
  - agm
  - handoff
  - todo
  - agent-git-mail
---

# AGM 下一步交接（2026-04-04）

## 当前阶段结果

### 1. AGM profile 模型主线已收口
- profile-first 设计、implementation plan、review 都已完成。
- `agent-git-mail` 中 AGM profile 模型代码已落地。
- Docker E2E 基础设施已完成，位于：
  - `packages/agm/test/docker/profile-e2e/`
- 当前 Docker E2E 验收边界收口为：
  - `daemon -> host adapter -> fake /internal/agm/ingress`
  - 不覆盖真实 HappyClaw runtime / Feishu 回流 / mailbox send-delivery 全链路。

### 2. Docker E2E 当前验证结果
- SUCCESS 路径：4/4 ✅
- FAILURE 路径：4/4 ✅
- Profile isolation：4/4 ✅
- 共 12 条断言通过

### 3. npm 发布状态
- `@t0u9h/agent-git-mail` 已发布到：
  - `0.1.12`

### 4. daemon lifecycle + launchd 方向已收口
已拍板新模型：

```bash
agm --profile <name> daemon run
agm --profile <name> daemon start
agm --profile <name> daemon stop
agm --profile <name> daemon status
```

关键原则：
- `run` 保留为前台核心执行语义
- macOS 上 `start/stop/status` 统一走 launchd
- AGM 不做自管 pid / fork / daemonize
- 状态真相源唯一为 launchd
- stdout/stderr 落 profile state 目录
- 非 macOS 上 `start/stop/status` 显式 unsupported

---

## 明天优先 TODO

## P0：继续收 AGM daemon lifecycle + launchd
先做最后一轮静态 + 实机验证，不要直接宣布完成。

### 要做的事
- 拉一遍 Hex 最新 daemon lifecycle 代码，做最后一轮静态 review
- 确认以下点在代码中真实成立：
  - `daemon run/start/stop/status` CLI 解析正确
  - launchd domain target / service target 正确区分
  - plist ProgramArguments 稳定，不依赖脆弱入口
  - 不再有双重日志重定向
  - status 真相源仍然唯一来自 launchd

### 如果静态 review 没新 blocker
安排一轮 **macOS 本机 smoke**：

```bash
agm --profile <name> daemon start
agm --profile <name> daemon status
agm --profile <name> daemon stop
```

重点验证：
- launchd job 真能拉起
- status 能看对
- stop 真能停掉
- stdout/stderr 真落到 profile state 目录

---

## P1：补 daemon lifecycle 的验证空白
今天已经明确：daemon lifecycle 不能只看代码和现有测试通过。

### 明天需要确认
- 有没有专门覆盖 `start/stop/status` 的测试
- 是否已经有真实 macOS smoke 证据
- 如果没有，就不能把 daemon lifecycle 说成 fully done

### 核心原则
> 没有真实 launchd smoke，不要轻易说 daemon lifecycle 完成。

---

## P1：收版本与仓库卫生
- npm 已发到 `0.1.12`
- 明天检查 `agent-git-mail` 工作树是否干净
- 特别注意：本地 `.env` 不能混入提交
- 如有必要，补 release/closure 说明

---

## P2：补 closure note（前提是 smoke 通过）
如果明天 daemon lifecycle smoke 通过，建议补一份 closure note，至少包含：
- 最终设计结论
- 实现边界
- 已验证内容
- 未覆盖内容
- 后续可选增强（systemd / restart / logs / CI）

---

## 当前明确未完成项
- daemon lifecycle 的 **macOS launchd 实机 smoke** 还没有形成最终验收结论
- 当前 Docker E2E 只到 fake ingress，不是完整宿主闭环
- AGM 还有一些“长期化 host integration 验证”未做透

---

## 明天的工作原则
- 先补验证，再谈完成
- 先看真实 launchd smoke，再做进一步收口
- 不要把“代码看起来差不多了”当成“已经真正能用”
