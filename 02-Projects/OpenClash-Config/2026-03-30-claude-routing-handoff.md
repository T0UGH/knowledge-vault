---
title: Claude 路由探测与接手说明（2026-03-30）
category: handoff
tags:
  - OpenClash
  - Claude
  - routing
  - handoff
  - Obsidian
summary: 记录 2026-03-30 晚上对 Claude 请求实际路由的探测结果、已确认结论、最小修正，以及下一位 agent 可直接继续的验证步骤。
---

# Claude 路由探测与接手说明（2026-03-30）

## 1. 这份文档是干什么的

这不是原理介绍，而是给**下一个接手的 agent / 人**看的现场交接。

目标是让接手方不用重新猜：

- 当前配置想实现什么
- 今晚到底测到了什么
- 哪些结论已经被实测确认
- 还差最后哪一步验证

如果下次再换一个不太聪明的 AI，它应该至少能按这份文档继续，不至于又从头怀疑 DNS、节点、fake-ip、Global 模式这些已经排过的点。

---

## 2. 当前目标架构

目标不是“Claude 能打开就行”，而是让 Claude 相关流量尽量稳定地走专用链路：

**客户端 → OpenClash → 第一层 SG → 第二层美国静态 IP → Claude**

对应配置文件：

- 工作副本：`/Users/haha/.openclaw/workspace/rook-clash.yaml`

配置中的关键组：

- `Claude-Dialer`：第一层 SG
- `Claude-US-SOCKS5`：第二层美国静态 IP（`107.180.144.40:45001`）
- `Claude-Chain`：Claude 专用策略组

---

## 3. 今晚已确认过的事实

### 3.1 最开始探测时，Claude 请求实际上走的是 GLOBAL

用户在 OpenClash 面板里看到的连接记录是：

```text
platform.claude.com:443 using GLOBAL
claude.ai:443 using GLOBAL
```

这说明当时并没有按专用规则分流，而是直接走全局代理出口。

这是一个已经确认过的事实，不要下次又重新猜“是不是规则没写对”。

### 3.2 当时机器侧的实测外显画像是台湾出口

在 **Global 模式**下，直接探测：

- `https://claude.ai/cdn-cgi/trace`
- `https://platform.claude.com/cdn-cgi/trace`
- `https://console.anthropic.com/cdn-cgi/trace`

得到的核心结果一度是：

```text
ip=141.11.42.64
loc=TW
colo=TPE
```

这说明当时暴露给 Cloudflare 的公网画像是 **TW**，不是目标中的美国静态 IP。

### 3.3 切回 Rule 模式后，`claude.ai` 已经成功走到美国静态 IP

用户把 OpenClash 从 Global 改回 Rule 之后，再次探测：

`https://claude.ai/cdn-cgi/trace`

得到：

```text
ip=107.180.144.40
loc=US
colo=LAX
```

这说明下面这些至少对 `claude.ai` 已经成立：

- `claude.ai` 规则成功命中 `Claude-Chain`
- `Claude-US-SOCKS5` 这条链能真正出网
- 最终外显已经是目标中的美国静态 IP

所以，**不要再回退去怀疑“整个 Claude 专用链根本没通”**。它至少对 `claude.ai` 是通的。

### 3.4 但 `platform.claude.com` 当时仍然走台湾出口

同一轮探测里：

`https://platform.claude.com/cdn-cgi/trace`

得到的仍是：

```text
ip=141.11.42.64
loc=TW
colo=TPE
```

这说明问题已经被收敛成：

> 不是整条 Claude 链没通，而是 `claude.ai` 和 `platform.claude.com` 没有被同一套域名规则完整接住。

---

## 4. 根因定位：不是同一棵域名树

接手方必须记住这个点：

- `claude.ai` 属于 `*.claude.ai`
- `platform.claude.com` 属于 `*.claude.com`

它们看起来都像 Claude，但对 Clash 规则来说不是同一棵域名树。

当晚检查 `rook-clash.yaml` 时，Claude 规则原本只有：

```yaml
DOMAIN-SUFFIX,anthropic.com,Claude-Chain
DOMAIN-SUFFIX,claude.ai,Claude-Chain
DOMAIN-SUFFIX,claudeusercontent.com,Claude-Chain
DOMAIN-SUFFIX,claudeusercontent.net,Claude-Chain
DOMAIN-SUFFIX,claude-api.com,Claude-Chain
DOMAIN-SUFFIX,claudecontentmoderation.com,Claude-Chain
```

也就是说：

- `claude.ai` 会命中 `Claude-Chain`
- 但 `platform.claude.com` 不会命中，因为当时没有 `claude.com` 这支规则

所以这不是“同一套规则没一起生效”，而是**规则覆盖面本来就不完整**。

---

## 5. 今晚已做的最小修正

已在 `/Users/haha/.openclaw/workspace/rook-clash.yaml` 中补入：

```yaml
DOMAIN-SUFFIX,claude.com,Claude-Chain
```

这是一处最小改动，目的是把：

- `platform.claude.com`
- 未来其他 `*.claude.com`

统一纳入 `Claude-Chain`。

对应工作区提交：

- `82762ff` — `fix: route claude.com via Claude-Chain`

---

## 6. 下一个接手者不要重复犯的错

### 不要重复怀疑 1：`198.18.0.x` 是不是错误出口

不是。

如果本机解析 `claude.ai` 得到：

```text
198.18.0.x
```

这通常只是 **fake-ip 生效**，不是最终公网出口。

### 不要重复怀疑 2：`claude.ai` 返回 403 challenge 说明链路有问题

也不是。

对 `https://claude.ai` 的 curl 返回过：

```text
HTTP/2 403
cf-mitigated: challenge
```

这说明：

- TLS 握手成功
- 请求已经到 Cloudflare
- 只是 curl 被 challenge

它不能用来证明“代理坏了”。

### 不要重复怀疑 3：整个 Claude 链都没通

这个也已经被实测推翻。

因为 `claude.ai/cdn-cgi/trace` 已经明确出现过：

```text
ip=107.180.144.40
loc=US
colo=LAX
```

所以链是通的，至少对 `claude.ai` 通。

### 真正应该优先检查的是

1. OpenClash 当前是不是 **Rule** 模式，而不是 **Global**
2. `Claude-Chain` 是否选中 `Claude-US-SOCKS5`
3. `Claude-Dialer` 是否选中某个可用 SG
4. `platform.claude.com` 是否已被 `DOMAIN-SUFFIX,claude.com,Claude-Chain` 接住

---

## 7. 下一步验证步骤（最小闭环）

在用户把新版 `rook-clash.yaml` 上传并应用后，优先跑这两条：

```bash
curl -sS https://claude.ai/cdn-cgi/trace
curl -sS https://platform.claude.com/cdn-cgi/trace
```

理想结果：

两条都应接近：

```text
ip=107.180.144.40
loc=US
colo=LAX
```

如果仍然分裂：

- `claude.ai` 是 US
- `platform.claude.com` 还是 TW

那么优先检查：

1. 路由器是否真的应用了**最新**配置，而不是旧缓存
2. OpenClash 面板的连接日志中，`platform.claude.com` 当前到底显示 `using` 什么组
3. 是否存在更前面的规则把 `claude.com` 抢走了
4. OpenClash / Mihomo 内核是否对当前规则顺序或 provider 合并有额外处理

---

## 8. 给下一个 agent 的一句话总结

到目前为止，问题已经被收敛得很小：

> Claude 专用二跳链本身不是没通；`claude.ai` 已能走美国静态 IP。当前只剩 `platform.claude.com` 这支是否已随着新增的 `DOMAIN-SUFFIX,claude.com,Claude-Chain` 一起收口，需要在用户重新上传配置后复测确认。
