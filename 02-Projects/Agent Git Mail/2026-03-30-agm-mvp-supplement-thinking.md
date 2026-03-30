# 2026-03-30 AGM MVP 增补版：当前思路收口

## 一、当前已经成立的部分

截至目前，`agent-git-mail` 的 MVP 核心链路已经成立：

### 1. AGM 本体已通过
在 Docker + 真实 GitHub 私有测试仓库下，已经验证通过：
- `send`
- `read/list`
- `reply`
- `archive`
- remote fresh clone 一致性

### 2. OpenClaw plugin 已通过到真实 session transcript
不是只停在 `enqueue_done`，而是已经验证到：
- 新信检测
- `enqueueSystemEvent`
- `requestHeartbeatNow()`
- heartbeat session 消费 system event
- session transcript 出现 AGM 通知文本

### 3. 当前 MVP 路由策略已收口
当前实现已经改成：
1. 配了 `AGM_FORCED_SESSION_KEY` → 用显式配置
2. 否则如果有 runtime session binding → 用 binding
3. 再否则默认回退到：`agent:main:main`

也就是：

> **默认发到 main session，但保留配置覆盖。**

---

## 二、当前暴露出来的真正缺口

MVP 已经不是“功能能不能跑”的问题，而是“产品语义和初始化是否收口”的问题。

### 缺口 1：配置模型缺少 `self identity`
当前 AGM 配置长这样：

```yaml
agents:
  mt:
    repo_path: /path/to/mt
  hex:
    repo_path: /path/to/hex
```

这说明当前配置模型更像：
- **机器级共享配置 / mailbox registry**
- 不是“单 agent 私有配置”

问题在于：
- 看不出“我是谁”
- 看不出当前安装实例对应哪个 mailbox
- 看不出当前通知默认应该服务谁

也就是说，当前配置表达的是：
> 这台机器知道有哪些 mailbox repo

而不是：
> 当前这个 agent/安装实例是谁，它自己的 mailbox repo 是哪个

这是一个明确的产品语义缺口。

---

### 缺口 2：没有通讯录 / address-book 层
从代码看，当前 `agm send --to xxx` 的真实含义是：
- 去 `config.agents[xxx]` 找 `repo_path`
- 然后直接把文件写进那个 repo 的 `inbox/`

也就是说，当前“收件人”并不是一个真正的通信对象抽象，而只是：
> 配置表里的一个名字，对应一个本地 repo path

当前缺少的是一层更清晰的“通讯录”语义，例如：
- 对方是谁
- 他的 mailbox repo 在哪里
- 这个地址是本地路径还是远程仓库语义
- 当前 agent 默认的联系人列表是什么

换句话说：

> 当前 CLI 有 send/reply，但没有真正的 address-book / contact model。

---

### 缺口 3：安装态不是“默认可用”
虽然当前路由已经有默认 main 兜底，但系统仍然不是安装即用。

原因不是 session routing，而是更基础的：
- 如果缺 `repo_path`
- 或配置文件里根本没有当前 agent 的 mailbox repo
- 那 plugin / daemon 还是不可用

也就是说：
- 默认 main 只解决“通知发去哪”
- 没解决“mailbox 从哪读”

所以当前真正的鸡蛋问题不是 session，而是：
> **repo bootstrap / repo mapping 还没有产品化。**

---

## 三、当前我们对方案的判断

### 1. 不先做重型自动 bootstrap
当前不建议一步到位做：
- 自动 clone repo
- 自动建仓
- 自动生成全部配置
- 自动发现所有 session / mailbox

原因：
- 这会把 MVP 增补版一下拉进产品化重构
- 复杂度会明显上升
- 当前最核心的问题还没有收得足够清楚

### 2. 先走轻量增补路线
当前更合理的思路是：

#### C：继续保留 runtime 默认兜底
- 默认发到 `main`
- 配置可以覆盖

#### 薄 B：增加最小 preflight / guardrail
目标不是 doctor，而是：
- 不 silent fail
- 缺配置时给明确 warning / error
- 告诉 agent / 用户：当前不可用，原因是什么

比如：
- 缺 `repo_path`
- repo 路径不存在
- repo 不是 git repo

然后：
- 当前这轮跳过该 mailbox source
- 下轮继续尝试
- 配置补齐后自动恢复

这个 guardrail 的意义是：
> **让系统在缺配置时可诊断，而不是半瘫静默。**

---

## 四、MVP 增补版最该解决的，不是更多能力，而是更清楚的语义

当前真正应该增补的，不是再做更多通知技巧，而是把下面三件事说清楚并落到实现：

### 1. 当前安装实例的身份是谁
也就是配置里要能表达：
- 我是谁
- 我的 mailbox repo 是哪个

### 2. 我如何给别人发信
也就是 CLI 需要补一层更清晰的“通讯录 / 联系人”语义，而不是让 `--to` 直接等价于 `config.agents[xxx]`

### 3. 缺配置时系统怎么表现
不能静默假成功，必须能明确告知：
- 缺什么
- 为什么当前不可用
- 该补哪一类配置

---

## 五、当前阶段可接受的 MVP 增补版方向

### 方向 A：配置模型轻量重构
从“机器级 mailbox registry”往“单安装实例语义”靠一点。

例如至少引入：

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

这不是最终版，但会比当前 `agents:` 更接近真实产品语义。

### 方向 B：增加薄 preflight
运行时只做最小检查：
- self mailbox repo 是否存在
- contacts 配置是否可解析
- repo path 是否存在且为 git repo

检查失败时：
- 不 crash
- 不静默成功
- 给明确 warning

### 方向 C：保留默认 main 路由
这条已经落地，作为当前 MVP 的通知默认策略是合理的：
- 最简单
- 最稳定
- 最接近用户预期

---

## 六、关于 bootstrap / install 脚本的当前设计判断

本轮进一步收口出的一个方向是：后续可以增加一个 **非交互式 bootstrap/install 脚本**，目标不是做万能安装器，而是把 AGM/OpenClaw 推进到“最小可用（至少能收通知）”状态。

当前对这个脚本的边界判断如下：

### 1. 目标状态
脚本执行完成后，应达到：
- 环境满足
- agm 已安装
- OpenClaw plugin 已安装
- 已写入最小默认配置
- 默认通知发到 `main`
- 至少能收通知

### 2. 输入方式
脚本应为 **非交互式**，主要给 agent / 自动化流程用，不依赖人类逐步输入。

当前倾向由环境变量/参数提供最小输入，例如：
- `AGM_SELF_ID`
- `AGM_SELF_REPO_PATH`
- 可选 `AGM_CONFIG_PATH`

### 3. 对系统依赖的策略
对系统级依赖：
- `git`
- `node`
- `npm`
- `openclaw`

当前判断为：
> **只检查，不自动安装。**

缺少任一关键依赖时，脚本应明确失败并退出，而不是尝试帮用户做系统层安装。

### 4. 对 AGM 自身的职责
脚本可以负责：
- 安装 `@t0u9h/agent-git-mail`
- 安装 `@t0u9h/openclaw-agent-git-mail`
- 写 AGM 默认配置

但当前不建议让脚本负责：
- 自动 clone mailbox repo
- 自动创建 remote repo
- 自动配置联系人/通讯录
- 自动发现所有 session

### 5. 这轮只保证“能收通知”，不保证“立即可发”
当前对 bootstrap 的目标收口为：
> **先做到最小可用（receive-ready），不把联系人/address-book 初始化塞进第一版脚本。**

也就是说：
- 执行完脚本后，系统至少能收通知
- 但给别人发信所需的 contact/address-book 配置，可以后续再补

### 6. 一个重要约束：脚本必须 self-safe，避免 OpenClaw 自杀式安装
这是本轮讨论中新增的关键设计约束。

如果这个脚本由 OpenClaw/agent 自己执行，而脚本又粗暴地：
- 覆盖当前配置
- reload/restart 当前 gateway
- 重装并立即切换运行时依赖

就可能导致当前会话中断或环境自毁。

因此当前判断是：

> **bootstrap/install 脚本必须满足 self-safe / idempotent 原则。**

具体应满足：
- 默认不自动重启 OpenClaw / gateway
- 重复执行应尽量 no-op
- 已初始化时应快速返回 success/no-op
- 只补 AGM 自己最小配置，不大范围覆盖现有 OpenClaw 主配置
- 不因执行脚本导致当前 session 中断

这意味着脚本应更像一个“无害 bootstrap”，而不是“强控制安装器”。

## 七、一句话收口

当前 AGM 的核心链路已经能跑，问题已经从“功能能不能用”转移到了：

> **配置语义是否清楚、联系人模型是否成立、缺配置时是否可诊断，以及 bootstrap 是否能安全把系统推进到最小可用。**

因此，MVP 增补版最值得做的不是大自动化，而是：

1. 补 `self identity`
2. 补通讯录 / contact 语义
3. 补薄 preflight / guardrail
4. 保持默认 main 路由
5. 设计一个 self-safe 的非交互式 bootstrap 脚本
