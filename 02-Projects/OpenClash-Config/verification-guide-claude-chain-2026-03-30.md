---
title: Claude 二跳链式代理验证指南（2026-03-30）
category: project-note
tags:
  - OpenClash
  - Claude
  - dialer-proxy
  - 验证
  - Mihomo
summary: 针对 rook-clash.yaml 中 Claude 专用二跳链式代理配置的上传后验证步骤，覆盖配置组选择、规则命中、链路验证与最终出口确认。
---

# Claude 二跳链式代理验证指南（2026-03-30）

## 一、目标

要验证的不是“Claude 能不能打开”，而是以下三件事：

1. **Claude 相关流量确实命中了 `Claude-Chain`**
2. **`Claude-US-SOCKS5` 确实通过 `Claude-Dialer` 建立连接**
3. **最终对 Claude 看起来像美国静态 IP，而不是普通机场出口**

---

## 二、当前前提

本地已完成的配置修改：

- 配置文件：`/Users/haha/.openclaw/workspace/rook-clash.yaml`
- 已启用：
  - `dns.enable: true`
  - `enhanced-mode: fake-ip`
  - 基础 `fake-ip-filter`
- 已新增 AI 专项显式规则
- 已新增 Claude 专用二跳链：
  - `Claude-Dialer`（第一层 SG）
  - `Claude-US-SOCKS5`（第二层美国静态 IP，使用 `dialer-proxy`）
  - `Claude-Chain`（Claude 专用策略组）

在开始验证前，**先把 `rook-clash.yaml` 上传到路由器/OpenClash 并应用。**

---

## 三、验证步骤

### Step 1：确认配置已成功载入

上传 `rook-clash.yaml` 后，先确认 OpenClash 没有报 YAML / 配置错误。

检查点：

- OpenClash 配置是否成功应用
- 内核是否正常运行
- 没有出现 `dialer-proxy`、组名、YAML 缩进等报错

如果这一步失败，后续验证全部无意义。

---

### Step 2：确认两个关键策略组的选项

进入 OpenClash 面板，找到以下两个组：

#### 1. `Claude-Chain`
应选中：

- `Claude-US-SOCKS5`

不要选成：

- `DIRECT`

#### 2. `Claude-Dialer`
应选中：

- 一个新加坡节点（SG）
- 例如先试：`SG | 新加坡 | 01`

这一步的意义：

- `Claude-Chain` 决定 Claude 流量最终是不是走美国静态 IP
- `Claude-Dialer` 决定美国静态 IP 是通过哪一个 SG 节点建立出来的

---

### Step 3：验证规则命中

打开 OpenClash / Clash 的：

- 连接（Connections）
- 或日志（Logs）

然后在浏览器访问：

- `https://claude.ai`

也可以顺手触发：

- `https://console.anthropic.com`

观察这些域名相关请求：

- `claude.ai`
- `anthropic.com`
- `claudeusercontent.com`
- `claude-api.com`

应命中：

- `Claude-Chain`

而不是：

- `极连云`
- `DIRECT`

如果命中的是 `Claude-Chain`，说明规则接管成功。

---

### Step 4：验证链式依赖是否真实存在

这一步不是看“能不能打开”，而是验证：

> `Claude-US-SOCKS5` 是否真的依赖 `Claude-Dialer`

#### 推荐做法：切换 `Claude-Dialer` 的 SG 节点

做法：

1. 先让 `Claude-Dialer` 选 `SG | 新加坡 | 01`
2. 测一次 `claude.ai`
3. 再切到另一个 SG 节点（如 `SG | 新加坡 | 02`）
4. 再测一次 `claude.ai`

如果切换 SG 后：

- Claude 访问表现随之变化（延迟、可用性、日志特征变化）

那说明这条链大概率不是摆设，而是真的依赖 `Claude-Dialer`。

#### 更强的做法（可选）

临时把 `Claude-Dialer` 切到一个明显不可用/较差节点，再测试 Claude：

- 如果 Claude 明显异常
- 而普通 OpenAI / 其他国外流量不受同样影响

那就更能证明 Claude 确实走了这条二跳链。

---

### Step 5：验证最终出口是不是美国静态 IP

这是最终目标验证，但不能直接用普通浏览器随便访问 IP 查询站来下结论，因为普通浏览器访问未必命中 Claude 规则。

#### 更可靠的验证思路

后续可追加一条临时测试规则，把一个测试域名也指向 `Claude-Chain`，例如：

- `ip.sb`
- `ifconfig.me`
- `ipinfo.io`

然后访问该测试域名，观察最终返回的出口 IP 是否为美国静态 IP。

**注意：** 当前配置里默认还没有加入这个测试域名规则，因此如果现在直接访问这些站，不一定走 Claude 链。

---

## 四、判断标准

### 成功标准

如果满足以下条件，可以认为 Claude 专用二跳链基本成立：

1. OpenClash 成功加载 `rook-clash.yaml`
2. `Claude-Chain` 选中了 `Claude-US-SOCKS5`
3. `Claude-Dialer` 选中了某个 SG 节点
4. `claude.ai` / `anthropic.com` 请求命中了 `Claude-Chain`
5. 切换 `Claude-Dialer` 时，Claude 访问行为能体现出依赖关系
6. 后续测试域名验证时，出口确实表现为美国静态 IP

### 失败信号

出现以下任一情况，说明还没真正跑通：

- OpenClash 无法加载配置
- `Claude-Chain` 实际走了 `DIRECT`
- Claude 请求仍然命中 `极连云`
- 切换 `Claude-Dialer` 对 Claude 完全无影响
- 最终出口仍显示为普通机场节点而不是美国静态 IP

---

## 五、推荐验证顺序（最省事版）

1. 上传并应用 `rook-clash.yaml`
2. 确认 `Claude-Chain -> Claude-US-SOCKS5`
3. 确认 `Claude-Dialer -> 某个 SG`
4. 访问 `claude.ai`
5. 在 OpenClash 面板看连接是否命中 `Claude-Chain`
6. 切换 `Claude-Dialer` 的 SG，再测一次 `claude.ai`
7. 如果命中和依赖都成立，再考虑加一个测试域名验证最终出口

---

## 六、当前建议

验证阶段先不要继续大改规则。

先做这三件事：

- **确认组选择正确**
- **确认规则命中正确**
- **确认链路依赖真实存在**

等这三步通过后，再决定是否增加“Claude 测试域名规则”来验证最终出口 IP。
