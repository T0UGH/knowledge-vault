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

## 六、一句话收口

当前 AGM 的核心链路已经能跑，问题已经从“功能能不能用”转移到了：

> **配置语义是否清楚、联系人模型是否成立、缺配置时是否可诊断。**

因此，MVP 增补版最值得做的不是大自动化，而是：

1. 补 `self identity`
2. 补通讯录 / contact 语义
3. 补薄 preflight / guardrail
4. 保持默认 main 路由
