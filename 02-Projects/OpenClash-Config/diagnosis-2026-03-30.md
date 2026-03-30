---
title: OpenClash 配置诊断（2026-03-30）
category: project-note
tags:
  - OpenClash
  - DNS
  - fake-ip
  - Codex
  - Claude
  - OpenAI
summary: 对 j.yaml 与 rook-clash.yaml 的诊断，重点关注 DNS 泄漏、fake-ip 是否真正生效，以及 AI 服务分流是否靠显式规则还是最终兜底。
---

# OpenClash 配置诊断（2026-03-30）

## 一、背景

贵平当前重点关注的是：

- 家用网络下使用 OpenClash
- 优先防范 DNS 泄漏
- 当前默认先不开 TUN
- 希望理解配置对 Claude / OpenAI / Codex 等 AI 服务究竟如何生效

## 二、关键发现

### 1. 原始配置 `j.yaml` 的最大问题：DNS 配置自相矛盾

原配置中同时存在：

```yaml
dns:
  enable: false
  enhanced-mode: fake-ip
  fake-ip-range: 198.18.0.1/16
  nameserver: [...]
  fallback: [...]
```

这意味着：

- 配置意图上想走 `fake-ip`
- 但又关闭了 Clash/OpenClash 对 DNS 的实际接管

结论：

> 这不是“fake-ip 没调好”，而是根本没有真正进入 fake-ip 工作闭环。

### 2. 基于原配置已复制出副本 `rook-clash.yaml`

本次只做了**最小改动**：

1. 将：

```yaml
dns:
  enable: false
```

改为：

```yaml
dns:
  enable: true
```

2. 增加基础 `fake-ip-filter`：

```yaml
fake-ip-filter:
  - '*.lan'
  - '*.local'
  - '*.arpa'
  - 'time.*.com'
  - 'time.*.apple.com'
  - 'time-ios.apple.com'
  - 'time1.cloud.tencent.com'
  - 'time.ustc.edu.cn'
  - 'pool.ntp.org'
  - 'localhost.ptlogin2.qq.com'
```

### 3. 当前 AI 服务的代理方式仍以“兜底”生效为主

在现有规则里，没有看到明确的：

- `anthropic.com`
- `claude.ai`
- `openai.com`
- `chatgpt.com`
- `codex` 相关显式规则

但由于规则末尾存在：

```yaml
MATCH,极连云
```

所以目前判断是：

- Claude / OpenAI / Codex 大概率会走代理
- 但主要依赖最终兜底，而不是显式 AI 专项规则

这类配置可以用，但不够精细。

## 三、当前判断

### 对 DNS 的判断

- 当前家用路由器 OpenClash 场景下，更值得优先尝试的是：
  - `dns.enable: true`
  - `enhanced-mode: fake-ip`
  - 合理 `fake-ip-filter`
- 不应直接照搬“本机 Clash Verge + 公司 VPN 场景”里的 `dns.enable: false`

### 对 TUN 的判断

- 家用 OpenClash 场景下，默认先不开 TUN
- 先把 DNS 接管、规则分流、AI 服务出口稳定性做好
- 只有在现有模式控制不住特定流量时，再考虑 TUN

### 对 AI 服务的判断

- 当前配置**大概率能用** Claude / OpenAI / Codex
- 但还没有达到“明确为 AI 服务专项优化过”的程度
- 后续建议补一段显式 AI 规则，而不是只依赖 `MATCH`

## 四、下一步建议

### 第一优先级
继续基于 `rook-clash.yaml` 迭代，而不是直接动原始 `j.yaml`。

### 第二优先级
在不重写整份配置的前提下，补一段 AI 专项显式规则，例如：

- `anthropic.com`
- `claude.ai`
- `claudeusercontent.com`
- `openai.com`
- `chatgpt.com`
- `oaistatic.com`
- `oaiusercontent.com`
- `auth0.com`
- `clerk.com`
- `challenges.cloudflare.com`

### 第三优先级
验证以下实际表现：

- DNS 是否真的由 OpenClash 接管
- Claude / ChatGPT / Codex 是否稳定
- fake-ip 是否引入局域网/Apple 副作用
- 是否需要退回 `redir-host`

## 五、项目意义

这不是一次性排障，而是一个长期配置优化项目。

目标不是“能翻”，而是：

- AI 服务链路更稳定
- DNS 与连接路径更自洽
- 配置更透明、可回滚、可验证
- 后续可以沉淀出一版适合贵平的家用 OpenClash for AI 模板

## 六、2026-03-30 第二轮配置改动

本轮继续基于 `rook-clash.yaml` 做**小步迭代**，没有重写整份配置，只新增了一组 **AI 专项显式规则**，用于避免 Claude / OpenAI / Codex 相关流量仅依赖 `MATCH,极连云` 兜底生效。

新增的显式规则包括：

- `anthropic.com`
- `claude.ai`
- `claudeusercontent.com`
- `claudeusercontent.net`
- `claude-api.com`
- `claudecontentmoderation.com`
- `openai.com`
- `chatgpt.com`
- `oaistatic.com`
- `oaiusercontent.com`
- `oaiusercontent.net`
- `auth0.com`
- `clerk.com`
- `challenges.cloudflare.com`

### 本轮改动的意义

1. **把 AI 服务从“靠 MATCH 兜底”升级为“明确受控”**
   - 更清楚知道 Claude / OpenAI / ChatGPT / Codex 相关链路会被显式送入 `极连云`

2. **降低依赖链误伤风险**
   - 登录、静态资源、验证链路不再只是“希望它们最后被代理”，而是直接纳入规则

3. **保持最小修改原则**
   - 没有重写现有 rules 大盘
   - 只是在 `rules:` 起始位置插入一小段 AI 专项规则

### 当前状态

截至本轮，`rook-clash.yaml` 的方向已经比原始 `j.yaml` 更接近“面向 AI 服务的家用 OpenClash 配置”：

- `dns.enable: true`
- `enhanced-mode: fake-ip`
- 已补基础 `fake-ip-filter`
- 已补 AI 专项显式规则

后续仍可继续观察：

- AI 服务是否更稳定
- fake-ip 是否带来局域网副作用
- 是否还需要继续收紧 DNS / fallback 行为
