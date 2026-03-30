---
title: 如何让你的 Claude 活得更久：网络篇
source: /Users/haha/Downloads/如何让你的Claude活得更久—网络篇.lark.md
category: reference
summary: 关于 Claude Code、Codex 与公司 VPN 并存时的代理、DNS、防风控与网络配置实操文档。
tags:
  - Claude Code
  - Codex
  - OpenClaw
  - 网络代理
  - VPN
  - Clash
  - 防风控
---

> 参考 [Claude Code 安全使用指南](https://github.com/sakurs2/safe-claude?tab=readme-ov-file)

## 引言：这篇文章要解决什么问题

很多同学日常工作离不开 Claude Code、OpenAI Codex 这些 AI 编程工具，同时公司有自己的 VPN 办公网络。问题很简单：**怎么稳定、安全、不封号地使用 Claude Code，同时不影响公司 VPN 办公？**

**先说结论：能做到，而且最终方案极其简洁。** 第一章就是完整的实操教程，照着做 5 分钟就能跑起来。

但如果你想知道 **为什么是这套方案** ——为什么 DNS 要关掉、为什么不用 TUN、为什么规则这么写——后面几章是完整的踩坑记录和技术推演。光是 DNS 问题就经历了 6 种方案的失败，最终发现一行 `dns: enable: false` 就是终极解法。

**安全** 和 **不封号** 是这里的关键词。最终目标，是尽量"扮演"一个真正生活在美国的同学。仅仅能连上 Claude 是不够的——如果你的 HTTPS 流量走美国 IP，但 DNS 查询从中国发出，这个矛盾对风控系统来说一目了然。**DNS 泄漏是代理方案最容易忽略、也最致命的安全隐患。**

> **阅读建议**：赶时间？只看第一章。想搞懂原理？从第二章开始。

---

## 第一章：实操教程——从零搭建，5 分钟跑起来

以下是可以直接复制粘贴的完整教程。背后的选型逻辑、踩坑经历和技术原理，后面几章会详细展开。

### Step 1：购买代理 IP

1. 打开[BrighData](https://brightdata.com/?ps_partner_key=Y2hlbnNvbmdiaW40MTQz&ps_xid=DnNrSeKpYBDB9w&gsxid=DnNrSeKpYBDB9w&gspk=Y2hlbnNvbmdiaW40MTQz&utm_source=affiliates&utm_campaign=Y2hlbnNvbmdiaW40MTQz&gclid=Cj0KCQjw1ZjOBhCmARIsADDuFTDQvKaw1H_04mNu4ylH0mvH6iNs8hjoInEjfmZL9kZ7bPpKdwOdPB4aAjgNEALw_wcB)，选择 ISP Proxies

1. 地区选 **United States**，先薅免费的，试试IP质量和网速，购买 1 个 IP（$4/月）

2. （Option）如果不满意，他还可以换IP，直接在**配置**里重新填表单更新就行。

3. 协议选 **SOCKS5**

4. 记下：IP 地址、端口、用户名、密码

### Step 2：验证 IP 纯净度

通过代理访问以下平台，确认 IP 干净：

<table col-widths="244,244,244">
    <tr>
        <td>平台</td>
        <td>地址</td>
        <td>达标标准</td>
    </tr>
    <tr>
        <td>Scamalytics</td>
        <td>scamalytics.com/ip</td>
        <td>Fraud Score < 10</td>
    </tr>
    <tr>
        <td>ping0.cc</td>
        <td>ping0.cc</td>
        <td>风控值标签为"纯净"</td>
    </tr>
    <tr>
        <td>IPQualityScore</td>
        <td>ipqualityscore.com</td>
        <td>无 VPN/Proxy 标记</td>
    </tr>
</table>

如果 Fraud Score > 30，联系 IPRoyal 客服免费更换 IP。

### Step 3：安装 Clash Verge

1. 下载 [Clash Verge Rev](https://github.com/clash-verge-rev/clash-verge-rev/releases)（macOS / Windows / Linux 全平台）

2. 安装后打开，进入 **Profiles** 页面

3. 点击 **New**，选择 **Local**，粘贴以下配置：

```yaml
# ============================================================
# Clash 最终配置：VPN 兼容 + AI 防风控
# 系统代理模式（无 TUN），与公司 VPN 完全不冲突
# ============================================================

mixed-port: 7897
allow-lan: false
mode: rule
log-level: info
external-controller: 127.0.0.1:9090
secret: <随便设一个密码>
ipv6: false

# ==================== 代理服务器 ====================
proxies:
  - name: US-Residential
    type: socks5
    server: <你的代理 IP>
    port: <端口>
    username: <用户名>
    password: <密码>

# ==================== 代理组 ====================
proxy-groups:
  - name: US-Proxy
    type: select
    proxies:
      - US-Residential

# ==================== DNS（关闭）====================
dns:
  enable: false

# ==================== 不开 TUN ====================
tun:
  enable: false

# ==================== 分流规则 ====================
rules:
  # 1. Anthropic / Claude
  - DOMAIN-SUFFIX,anthropic.com,US-Proxy
  - DOMAIN-SUFFIX,claude.ai,US-Proxy
  - DOMAIN-SUFFIX,claudeusercontent.com,US-Proxy
  - DOMAIN-SUFFIX,claude-api.com,US-Proxy
  - DOMAIN-SUFFIX,claudecontentmoderation.com,US-Proxy

  # 2. OpenAI / ChatGPT / Codex
  - DOMAIN-SUFFIX,openai.com,US-Proxy
  - DOMAIN-SUFFIX,chatgpt.com,US-Proxy
  - DOMAIN-SUFFIX,oaiusercontent.com,US-Proxy
  - DOMAIN-SUFFIX,oaistatic.com,US-Proxy
  - DOMAIN-SUFFIX,oaiusercontent.net,US-Proxy
  - DOMAIN-SUFFIX,sora.com,US-Proxy
  - DOMAIN-SUFFIX,openaiapi-site.azureedge.net,US-Proxy

  # 3. Apple
  - DOMAIN-SUFFIX,apple.com,US-Proxy
  - DOMAIN-SUFFIX,apple.co,US-Proxy
  - DOMAIN-SUFFIX,apple.news,US-Proxy
  - DOMAIN-SUFFIX,apple-dns.net,US-Proxy
  - DOMAIN-SUFFIX,icloud.com,US-Proxy
  - DOMAIN-SUFFIX,icloud.com.cn,US-Proxy
  - DOMAIN-SUFFIX,icloud-content.com,US-Proxy
  - DOMAIN-SUFFIX,itunes.com,US-Proxy
  - DOMAIN-SUFFIX,mzstatic.com,US-Proxy
  - DOMAIN-SUFFIX,apple-cloudkit.com,US-Proxy
  - DOMAIN-SUFFIX,apple-intelligence.com,US-Proxy
  - DOMAIN-SUFFIX,cdn-apple.com,US-Proxy
  - DOMAIN-SUFFIX,aaplimg.com,US-Proxy
  - DOMAIN-SUFFIX,push.apple.com,US-Proxy
  - IP-CIDR,17.0.0.0/8,US-Proxy,no-resolve

  # 4. WhatsApp
  - DOMAIN-SUFFIX,whatsapp.com,US-Proxy
  - DOMAIN-SUFFIX,whatsapp.net,US-Proxy
  - DOMAIN-SUFFIX,wa.me,US-Proxy

  # 5. 认证 / 支付网关
  - DOMAIN-SUFFIX,auth0.com,US-Proxy
  - DOMAIN-SUFFIX,clerk.com,US-Proxy
  - DOMAIN-SUFFIX,stripe.com,US-Proxy
  - DOMAIN-SUFFIX,stripe.network,US-Proxy
  - DOMAIN-SUFFIX,recaptcha.net,US-Proxy
  - DOMAIN-SUFFIX,hcaptcha.com,US-Proxy
  - DOMAIN-SUFFIX,turnstile.com,US-Proxy
  - DOMAIN,challenges.cloudflare.com,US-Proxy

  # 6. KEYWORD 兜底（不兜底 google/apple，避免误伤）
  - DOMAIN-KEYWORD,anthropic,US-Proxy
  - DOMAIN-KEYWORD,claude,US-Proxy
  - DOMAIN-KEYWORD,openai,US-Proxy
  - DOMAIN-KEYWORD,chatgpt,US-Proxy
  - DOMAIN-KEYWORD,codex,US-Proxy
  - DOMAIN-KEYWORD,sora,US-Proxy
  - DOMAIN-KEYWORD,icloud,US-Proxy
  - DOMAIN-KEYWORD,whatsapp,US-Proxy

  # 7. 其他全部直连
  - MATCH,DIRECT
```

1. 保存后，在 Clash Verge 主界面开启 **System Proxy**

2. **不要开 TUN**

### Step 4：配置 CLI 工具代理

Claude Code 和 Codex 是 CLI 工具，不读系统代理，需要单独配置。把以下内容加到 `~/.zshrc`：

```bash
run_with_proxy() {
    (
        cmd=$1; shift
        export http_proxy="http://127.0.0.1:7897"
        export https_proxy="http://127.0.0.1:7897"
        export all_proxy="socks5h://127.0.0.1:7897"
        export HTTP_PROXY="http://127.0.0.1:7897"
        export HTTPS_PROXY="http://127.0.0.1:7897"
        export ALL_PROXY="socks5h://127.0.0.1:7897"
        export no_proxy="localhost,127.0.0.1,::1,.byted.org,.bytedance.net,.bytedance.com,.byteintl.com,.byteintl.net,.volces.com,.snssdk.com,.bytedance-boe.net,.bytedance.org,.feishu.cn,.feishu.net,.larksuite.com,.larkoffice.com"
        export NO_PROXY="$no_proxy"
        command "$cmd" "$@"
    )
}

alias claude="run_with_proxy claude"
alias codex="run_with_proxy codex"
```

保存后执行 `source ~/.zshrc`。

**这段脚本的设计要点**：

- **子 Shell** `( )`：所有环境变量修改在子进程中，退出后自动消失，即使 Ctrl+C 中断也不会污染父 Shell

- `socks5h`：`h` 表示 DNS 解析也交给代理服务器，与 `dns: enable: false` 理念一致，确保零 DNS 泄漏

- **大小写双写**：`http_proxy` + `HTTP_PROXY`，部分程序只认其中一种

- `no_proxy`：Claude Code 工作时会派生 `git`、`npm`、`curl` 等子进程，它们继承代理环境变量。`no_proxy` 确保这些子进程访问内网域名时不绕路美国

![board_IUrOwFK7BhbzHJb3ggvcGk2dnqb](board_IUrOwFK7BhbzHJb3ggvcGk2dnqb.drawio)

### Step 5：验证

```bash
# 1. 验证代理生效（应返回美国 IP）
curl -x socks5h://127.0.0.1:7897 https://ifconfig.me

# 2. 验证 Claude API 可达（应返回 401 而非超时）
curl -I https://api.anthropic.com

# 3. 验证内网正常（VPN 开启状态下）
curl -I https://code.byted.org

# 4. 启动 Claude Code
claude
```

### Step 6：日常使用

1. 打开 Clash Verge → 开启 **System Proxy**

2. Claude Desktop / ChatGPT / WhatsApp → 自动走代理

3. 终端输入 `claude` 或 `codex` → 自动挂代理启动

4. 公司 VPN 随时开关，互不干扰

5. 其他所有网站和内网 → 直连，不受任何影响

> 以上就是完整教程。接下来的章节解释 **为什么这么做** ——选型过程、踩坑记录、和背后的技术原理。

---

## 第二章：方案选型——三条路，两条死胡同

### 我需要什么

在动手之前，先明确核心需求和优先级：

1. **100% 原生体验**——不做功能阉割，Extended Thinking、Tool Use、多步编排全部可用

2. **不封号**——这是底线，IP 必须干净，行为必须合规

3. **与公司 VPN 共存**——VPN 管内网，代理管 AI，互不干扰

4. **配置简单**——能一个工具解决的，不用两个

### 路线 A：Sub2API——用订阅额度当 API 用

Sub2API 的原理是在 Claude Code 和 claude.ai 之间架一个翻译层，把 API 请求转成 Web 请求。本质上是利用订阅（固定月费）和 API（按量计费）之间的价差做套利。

**为什么放弃？**

Anthropic 后端运行着两套完全独立的服务体系——API 服务（api.anthropic.com）和 Web 服务（claude.ai），认证方式、请求端点、请求格式、响应格式全部不同。Sub2API 被迫做有损的协议转译，Extended Thinking 无法映射，Tool Use 多步编排转译可能出错。更关键的是，这违反 Anthropic 服务条款，账号有封禁风险。

**结论：❌ 体验受损 + 合规风险，放弃。**

### 路线 B：美国云服务器 + Nginx 反代

在美国买一台 VPS（$3.5/月），搭 Nginx 反向代理，把 Claude Code 的 API 请求转发到 Anthropic 官方 API。纯透传，100% 原生体验，完全合规。

**为什么没选？**

技术上没问题，但需要额外维护一台云服务器，多了一个故障点。后来发现了更简单的方案，这条路就没必要走了。

**结论：✅ 可行，但有更优解。**

### 路线 C：Clash 系统代理 + 美国住宅 IP（最终方案）

调研过程中发现一个关键事实：**代理服务器的 IP 可以从中国大陆直连**，不需要中间跳板。这意味着不需要云服务器，不需要 Nginx，Clash 一个客户端按域名分流就够了。

架构极简：

```c
Claude Code / ChatGPT / WhatsApp
    │
    ▼ （系统代理）
Clash（mixed-port 7897）
    │
    ├── AI/Apple/WhatsApp 域名 → 美国住宅代理 → 目标服务
    └── 其他 → DIRECT（本地网络 / 公司 VPN）
```

**结论：✅ 最优解。**

![board_TdiswalDbhns43bcJapcsATBnDt](board_TdiswalDbhns43bcJapcsATBnDt.drawio)

### 选型的核心原则

回顾这三条路线，决策背后有三条原则：

<table col-widths="244,244,244">
    <tr>
        <td>原则</td>
        <td>含义</td>
        <td>体现</td>
    </tr>
    <tr>
        <td>**安全第一**</td>
        <td>宁可多花钱，不能封号</td>
        <td>放弃 Sub2API（违反 ToS），选择住宅 IP（Fraud Score 0），解决 DNS 泄漏（访问 IP 与 DNS 来源不一致 = 自报家门）</td>
    </tr>
    <tr>
        <td>**原生优先**</td>
        <td>不做任何功能阉割</td>
        <td>放弃 Sub2API（有损转译），选择纯透传方案</td>
    </tr>
    <tr>
        <td>**极简至上**</td>
        <td>能一个工具的不用两个，能一行配置的不写十行</td>
        <td>放弃云服务器方案，选择 Clash 单客户端；最终用 `dns: enable: false` 一行替代几十行 DNS 配置</td>
    </tr>
</table>

---

## 第三章：代理 IP 选型——最后一道防线

方案确定了，但代理 IP 的质量直接决定封号风险。这是最后一道防线。

### 为什么选 Static Residential

代理 IP 大致分三档：

<table col-widths="244,244,244">
    <tr>
        <td>类型</td>
        <td>特点</td>
        <td>风险</td>
    </tr>
    <tr>
        <td>数据中心 IP</td>
        <td>便宜，但被标记为 DC IP</td>
        <td>高——AI 服务直接封</td>
    </tr>
    <tr>
        <td>Rotating Residential</td>
        <td>住宅 IP，但每次请求换一个</td>
        <td>中——IP 行为不连续，可能触发风控</td>
    </tr>
    <tr>
        <td>**Static Residential**</td>
        <td>住宅 IP，固定不换</td>
        <td>**低——行为接近真实家庭用户**</td>
    </tr>
</table>

最终选择 **IPRoyal Static Residential**，$4/月，关键在于 **Static**：IP 固定不轮换，一旦确认干净就可以一直使用。

### 纯净度验证

买了 IP 不能直接用，要先验证纯净度。用多个平台交叉验证：

<table col-widths="183,183,183,183">
    <tr>
        <td>检测平台</td>
        <td>指标</td>
        <td>结果</td>
        <td>含义</td>
    </tr>
    <tr>
        <td>Scamalytics</td>
        <td>Fraud Score</td>
        <td>**0**</td>
        <td>最低风险，无欺诈特征</td>
    </tr>
    <tr>
        <td>ping0.cc</td>
        <td>风控值</td>
        <td>**20% - 纯净**</td>
        <td>低风控，住宅级别</td>
    </tr>
    <tr>
        <td>通用 IP 检测</td>
        <td>Risk Score</td>
        <td>**0 / Low**</td>
        <td>未被标记为代理或 VPN</td>
    </tr>
</table>

> **常见误区**：ping0.cc 的风控值 20% 不是"只有 20% 纯净"的意思。它是风险评分，数值越低越好，20% 对应的标签就是"纯净"。

### 维护建议

- 每 1-2 个月去 Scamalytics 复查 Fraud Score

- 如果分数明显上升，联系 IPRoyal 客服免费更换 IP

- 备选代理商：NodeMaven（$3-5/月，性价比最高）、Bright Data（$10-15/月，行业标杆）

---

## 第四章：核心挑战——DNS 泄漏与 VPN 共存

### 为什么 DNS 是这个方案的命门

Clash 配好规则、代理 IP 验证干净——到这一步，很多教程就结束了。但真正的风险藏在一个大多数人忽略的地方：**DNS 泄漏**。

什么意思？你的 HTTPS 流量确实通过美国住宅 IP 到达了 Anthropic 的服务器，但在此之前，你的设备需要先把 `api.anthropic.com` 这个域名解析成 IP 地址。这个 DNS 查询请求，如果从中国大陆的 DNS 服务器发出，就形成了一个致命矛盾：

> **访问来自美国 IP，但 DNS 查询来自中国 IP。**

![board_MkqXwlVy0hxA5FbGRPFcvlVkn8c](board_MkqXwlVy0hxA5FbGRPFcvlVkn8c.drawio)

这对 Anthropic 的风控系统来说，是一个非常明显的信号——真正的美国家庭用户，DNS 和访问 IP 应该在同一个地理位置。DNS 泄漏等于自报家门：我在用代理。

所以我必须解决 DNS 问题。而这一解决，就撞上了公司 VPN，开始了整整一天的排查。

### 第一个尝试：开启 Clash DNS，接管所有解析

最直觉的方案——既然怕 DNS 泄漏，那就让 Clash 接管 DNS 解析，把 DNS 查询也通过加密通道发出去。

```yaml
dns:
  enable: true
  enhanced-mode: fake-ip
  nameserver:
    - https://dns.google/dns-query
    - https://cloudflare-dns.com/dns-query
```

**结果：三个问题接踵而至。**

![board_BWRewBP6hhCPICbieaVc9XPdnOQ](board_BWRewBP6hhCPICbieaVc9XPdnOQ.drawio)

**问题一：网速变慢。** DNS 全部走海外 DoH（dns.google、cloudflare-dns.com），在中国大陆直连延迟极高。每次打开国内网站都要等几秒钟。

**解法**：国内域名用国内 DNS（阿里 223.5.5.5），海外域名走 fallback。网速问题解决了，但更大的问题来了。

**问题二：开了 VPN 后国内网站全挂。** 公司 VPN 劫持了所有 UDP 53 端口的 DNS 流量。DoT/DoH 加密 DNS 部分能绕过，但 `fake-ip` 模式注入的假 IP 触发了 VPN 的路由劫持——VPN 把假 IP 当真 IP 去路由，自然全挂。

**解法**：改用 `redir-host` 替代 `fake-ip`，避免注入假 IP。VPN 兼容性改善了，但真正的 boss 还在后面。

**问题三（Boss 战）：内网域名全部解析失败。** `codebase-api.byted.org`、`code.byted.org` 等字节内网域名全部报 `dns resolve failed: couldn't find ip`。

**根因**：Clash 开启 DNS 后会**接管所有 DNS 解析**。但字节内网域名只有公司 VPN 的 DNS 服务器能解析，Clash 配置的任何公共 DNS 都查不到这些域名。

我尝试了 6 种方案绕过这个问题，全部失败或不完美：

<table col-widths="183,183,183,183">
    <tr>
        <td>#</td>
        <td>方案</td>
        <td>结果</td>
        <td>死因</td>
    </tr>
    <tr>
        <td>1</td>
        <td>nameserver-policy 用 `[system]`</td>
        <td>❌</td>
        <td>macOS 兼容性问题</td>
    </tr>
    <tr>
        <td>2</td>
        <td>nameserver-policy 用 `127.0.0.1:53`</td>
        <td>❌</td>
        <td>Clash 自身监听 1053，形成 DNS 环路</td>
    </tr>
    <tr>
        <td>3</td>
        <td>nameserver-policy 用 `dhcp://en0`</td>
        <td>❌</td>
        <td>配置复杂且不稳定</td>
    </tr>
    <tr>
        <td>4</td>
        <td>rules 里加 DIRECT 放在代理规则前</td>
        <td>❌</td>
        <td>**Clash DNS 在规则匹配之前执行**，规则还没匹配到 DIRECT，DNS 就已经失败了</td>
    </tr>
    <tr>
        <td>5</td>
        <td>Clash Verge bypass 排除内网域名</td>
        <td>⚠️</td>
        <td>字节内网域名太多（byted.org, bytedance.net, volces.com...），穷举不完</td>
    </tr>
    <tr>
        <td>6</td>
        <td>PAC 模式只代理特定域名</td>
        <td>⚠️</td>
        <td>Trae（字节 IDE）启动时缓存 PAC，切换后不生效；一度卡死无法关闭</td>
    </tr>
</table>

### 顿悟：不是 DNS 怎么配的问题，是该不该让 Clash 碰 DNS 的问题

6 种方案全部失败后，我停下来重新审视整个思路。之前一直在想怎么把 Clash DNS 配对，但真正的问题是——**Clash 到底需不需要做 DNS？**

回到最初的安全诉求：我怕的是 DNS 泄漏，也就是访问 AI 服务时 DNS 查询从中国发出。那换个角度想：

- **代理域名**（anthropic.com 等）→ 流量通过 SOCKS5 代理发往美国。如果用 `socks5h`（h = hostname），**域名本身就被发送给代理服务器，由美国那端做 DNS 解析**。本地根本不发 DNS 查询。

- **直连域名**（内网、国内网站）→ 走 DIRECT，由系统/VPN 的 DNS 进行解析。这些域名本来就不怕泄漏。

也就是说，在系统代理模式下，**只要规则写对了，DNS 泄漏根本不会发生**——需要保护的域名，DNS 在美国解析；不需要保护的域名，DNS 在本地解析。Clash 不需要碰 DNS，因为它收到的是域名字符串（HTTP CONNECT），规则匹配只需要字符串比对。

### 终极解法：`dns: enable: false`

```yaml
dns:
  enable: false
```

一行配置，同时解决三个问题：

<table col-widths="244,244,244">
    <tr>
        <td>问题</td>
        <td>之前的方案</td>
        <td>现在</td>
    </tr>
    <tr>
        <td>DNS 泄漏</td>
        <td>开启 Clash DNS + DoH 加密</td>
        <td>代理域名由美国服务器解析，零本地泄漏</td>
    </tr>
    <tr>
        <td>VPN 冲突</td>
        <td>改 redir-host + 各种 fallback</td>
        <td>不接管 DNS = 不冲突</td>
    </tr>
    <tr>
        <td>内网域名</td>
        <td>6 种方案全失败</td>
        <td>直连域名自动走系统/VPN DNS</td>
    </tr>
</table>

**为什么之前没想到？** 因为直觉上"关掉 DNS"听起来很危险——域名不解析了怎么上网？但关键在于理解系统代理模式的工作原理：Clash 收到的是域名而不是 IP，规则匹配只需要字符串比对，真正的 DNS 解析交给上游。这不是"关掉 DNS"，而是**让正确的人做 DNS**。

![board_IH4IwUgn6hRqcCb0EMmcfV01nyg](board_IH4IwUgn6hRqcCb0EMmcfV01nyg.drawio)

> 有时候，最好的解决方案不是配更多东西，而是去掉多余的东西。

### 附：macOS 系统代理残留坑

在反复切换 bypass/PAC/系统代理模式的过程中，macOS「系统设置 → 网络 → Wi-Fi → 代理」中的配置不会自动清理。Clash Verge 重置后，macOS 系统层面的 bypass 列表还在，一度让排查方向走偏。

> **教训**：在 macOS 上调试代理配置时，每次大改之后都去系统设置里检查一遍代理配置是否残留。

---

## 第五章：规则设计哲学——为什么这么写

### 三层防护体系

<table col-widths="183,183,183,183">
    <tr>
        <td>层级</td>
        <td>规则类型</td>
        <td>作用</td>
        <td>示例</td>
    </tr>
    <tr>
        <td>第一层</td>
        <td>DOMAIN-SUFFIX</td>
        <td>精确匹配已知域名和所有子域名</td>
        <td>`DOMAIN-SUFFIX,anthropic.com` 覆盖 `api.anthropic.com`、`cdn.anthropic.com` 等</td>
    </tr>
    <tr>
        <td>第二层</td>
        <td>DOMAIN</td>
        <td>精确匹配单个域名（用于避免误伤）</td>
        <td>`DOMAIN,challenges.cloudflare.com`（不代理整个 cloudflare.com）</td>
    </tr>
    <tr>
        <td>第三层</td>
        <td>DOMAIN-KEYWORD</td>
        <td>兜底匹配可能遗漏的域名</td>
        <td>`DOMAIN-KEYWORD,anthropic` 兜住未来新增的子域名</td>
    </tr>
</table>

![board_MfwVwYodThKCotblvehcmYHOnGg](board_MfwVwYodThKCotblvehcmYHOnGg.drawio)

### 为什么不兜底 google 和 apple？

`DOMAIN-KEYWORD,apple` 会匹配 `pineapple-recipes.com`，`DOMAIN-KEYWORD,google` 会匹配 `google-analytics.com`（国内大量网站在用）。这两个词太宽泛，容易误伤，只靠 DOMAIN-SUFFIX 精确覆盖。

### 为什么精简掉了 DOMAIN 规则？

`DOMAIN-SUFFIX,anthropic.com` 已经匹配 `api.anthropic.com`、`console.anthropic.com`、`cdn.anthropic.com` 等所有子域名。再写 `DOMAIN,api.anthropic.com` 是纯冗余。精简规则 = 减少维护成本 + 降低出错概率。

### 为什么 Cloudflare 只代理一个域名？

`challenges.cloudflare.com` 是 Cloudflare Turnstile/人机验证的核心域名。如果把整个 `cloudflare.com` 代理了，国内大量用 CF CDN 加速的正常网站全会绕路美国，严重影响访问速度。所以用 `DOMAIN`（精确匹配单个域名）而不是 `DOMAIN-SUFFIX`。

### `no-resolve` 是什么？

Apple 的 `IP-CIDR,17.0.0.0/8,US-Proxy,no-resolve` 规则中，`no-resolve` 告诉 Clash 不要为了匹配这条 IP 规则去做 DNS 解析。在 `dns: enable: false` 下这是必须的，否则 Clash 会报错。实际上 Apple 的域名已经被前面的 DOMAIN-SUFFIX 规则匹配了，这条 IP-CIDR 只是兜底。

### `secret` 是什么？

`external-controller: 127.0.0.1:9090` 是 Clash 的 REST API 端口，Clash Verge 的仪表盘通过它控制 Clash 内核。如果不设 `secret`，本机任何程序都能调用这个 API 修改 Clash 配置——比如偷偷加代理、改规则。加了 secret 后，所有请求必须带 `Authorization: Bearer <密码>` 才会被接受。浏览器直接打开 `127.0.0.1:9090` 看不到页面是正常的——它是 JSON API，不是网页。

---

## 第六章：关键 Tradeoff 总结

回顾整个方案，有几个关键的 tradeoff 值得记录：

### Tradeoff 1：TUN 模式 vs 系统代理

<table col-widths="244,244,244">
    <tr>
        <td>维度</td>
        <td>TUN 模式</td>
        <td>系统代理</td>
    </tr>
    <tr>
        <td>流量覆盖</td>
        <td>全局（包括不走代理的 UDP 流量）</td>
        <td>仅 HTTP/SOCKS</td>
    </tr>
    <tr>
        <td>VPN 兼容</td>
        <td>❌ 抢路由表</td>
        <td>✅ 不同层级，互不干扰</td>
    </tr>
    <tr>
        <td>DNS 控制</td>
        <td>完全接管</td>
        <td>可以不接管（dns: false）</td>
    </tr>
    <tr>
        <td>配置复杂度</td>
        <td>高</td>
        <td>低</td>
    </tr>
</table>

**选择系统代理**：我们的场景只需要代理 HTTP/HTTPS 流量，不需要全局接管。系统代理更简单，且天然与 VPN 兼容。

![board_OOTQwWD0WhdYk3bGOCGc0MUNnuh](board_OOTQwWD0WhdYk3bGOCGc0MUNnuh.drawio)

### Tradeoff 2：dns: enable: true vs false

<table col-widths="244,244,244">
    <tr>
        <td>维度</td>
        <td>DNS 开启</td>
        <td>DNS 关闭</td>
    </tr>
    <tr>
        <td>域名解析控制</td>
        <td>Clash 完全接管</td>
        <td>交给上游（代理服务器 / 系统 DNS）</td>
    </tr>
    <tr>
        <td>DNS 泄漏风险</td>
        <td>低（需正确配置 DoH）</td>
        <td>**零**（代理域名在美国解析）</td>
    </tr>
    <tr>
        <td>内网域名</td>
        <td>需要逐一配置 nameserver-policy</td>
        <td>自动走系统/VPN DNS</td>
    </tr>
    <tr>
        <td>配置复杂度</td>
        <td>几十行</td>
        <td>一行</td>
    </tr>
</table>

**选择 DNS 关闭**：回到核心安全诉求——防止 DNS 泄漏暴露代理行为。开启 Clash DNS + DoH 可以解决泄漏问题，但需要几十行配置，还会引发 VPN 冲突和内网域名解析失败。关闭 DNS 后，代理域名由美国服务器解析（零泄漏），直连域名由系统 DNS 解析（内网兼容）。一行配置，更安全，更简单。

### Tradeoff 3：KEYWORD 兜底 vs 精确规则

<table col-widths="244,244,244">
    <tr>
        <td>维度</td>
        <td>纯 DOMAIN-SUFFIX</td>
        <td>DOMAIN-SUFFIX + KEYWORD</td>
    </tr>
    <tr>
        <td>覆盖率</td>
        <td>只覆盖已知域名</td>
        <td>兜住未来新增域名</td>
    </tr>
    <tr>
        <td>误伤风险</td>
        <td>零</td>
        <td>低（不兜底 google/apple）</td>
    </tr>
    <tr>
        <td>维护成本</td>
        <td>新域名需手动添加</td>
        <td>通常自动覆盖</td>
    </tr>
</table>

**选择两层防护**：封号风险远大于误伤风险。宁可多代理几个不相关的域名（最多是速度慢一点），也不能漏掉 AI 服务的域名。但对 google 和 apple 这种高频宽泛词不兜底，避免大面积误伤。

### Tradeoff 4：Static IP vs Rotating IP

<table col-widths="244,244,244">
    <tr>
        <td>维度</td>
        <td>Static Residential</td>
        <td>Rotating Residential</td>
    </tr>
    <tr>
        <td>IP 稳定性</td>
        <td>固定，长期使用</td>
        <td>每次请求可能换 IP</td>
    </tr>
    <tr>
        <td>行为模式</td>
        <td>像真人（固定 IP 上网）</td>
        <td>像爬虫（频繁换 IP）</td>
    </tr>
    <tr>
        <td>风控风险</td>
        <td>低——一旦验证干净可长期使用</td>
        <td>中——下次可能分到脏 IP</td>
    </tr>
    <tr>
        <td>价格</td>
        <td>$4/月</td>
        <td>按流量计费</td>
    </tr>
</table>

**选择 Static**：AI 服务的风控系统会分析 IP 行为模式。一个 IP 长期稳定使用，行为接近真实家庭用户，风控评分会越来越低。频繁换 IP 反而可疑。

---

## 第七章：经验教训——九条血泪总结

1. **dns: enable: false&nbsp;是终极解法**——在系统代理模式下，Clash 收到的是域名字符串而非 IP，规则匹配只需字符串比对。关闭 Clash DNS 后，代理域名由远端服务器解析（零泄漏），直连域名由系统/VPN DNS 解析（内网兼容），一行配置解决了 DNS 速度、VPN 冲突、内网域名三大问题

2. **redir-host 是中间方案**——`fake-ip` 注入假 IP 触发 VPN 路由劫持，`redir-host` 避免了冲突但仍需 Clash 做 DNS 解析。最终被 `dns: enable: false` 彻底取代

3. **DOMAIN-SUFFIX 覆盖子域名**——`DOMAIN-SUFFIX,anthropic.com` 已匹配所有子域名，不需要单独的 DOMAIN 规则。精简规则 = 减少维护成本 + 降低出错概率

4. **KEYWORD 规则是安全网**——但不兜底 `google` 和 `apple` 这种宽泛关键词，避免误伤

5. **系统代理模式足够用**——不需要 TUN 的全局流量接管，系统代理 + 域名规则就能精准分流

6. **CLI 和 GUI 的代理机制不同**——Electron 桌面 App 读取系统代理，CLI 工具需要通过环境变量单独配置

7. **代理 IP 质量决定封号风险**——Static Residential IP + Scamalytics Fraud Score 0 是最后一道防线

8. **macOS 系统代理设置会残留**——切换代理模式后，必须手动检查「系统设置 → 网络 → 代理」

9. **external-controller 要加 secret**——Clash REST API 不设密码 = 本机任何程序都能改你的代理配置

---

## 结语

从最初的"怎么在中国用 Claude Code"这个简单问题出发，到最终的 44 条规则 + 一行 `dns: enable: false`，中间经历了 3 条路线的评估、6 种 DNS 方案的失败、无数次 VPN 冲突的排查。

最终方案的优雅之处在于：**它什么都不做**。不做 DNS 解析，不创建虚拟网卡，不劫持路由表。只做一件事——根据域名字符串分流，让正确的上游去处理剩下的事情。

有时候，最好的解决方案不是加更多东西，而是去掉多余的东西。

下一步，需要进一步加强账号安全性，例如浏览器环境，系统时区，语言等等，让 Anthropic 认为我就是一个生活在美国的普通人。
