<!-- BLOCK_1 | IctCdR31Ron8KvxQNX6lqSIfgUd -->
> From: https://github.com/instructkr/claude-code
> 
<!-- END_BLOCK_1 -->

<!-- BLOCK_2 | doxlghDtY98hJQJf0gvGCBWwAmh -->
# Claude Code 封号机制深度探查报告<!-- 标题序号: 1 --><!-- END_BLOCK_2 -->

<!-- BLOCK_3 | Jsa2dZP4IowtwdxxzpwlDLTygXd -->
> 基于 Claude Code 源代码逆向分析，全面揭示其数据采集、上报机制及封号触发点
> 
<!-- END_BLOCK_3 -->

<!-- BLOCK_4 | doxlg8p7eeaL1zSAwFCWpawiggb -->
## 一、核心结论<!-- 标题序号: 1.1 --><!-- END_BLOCK_4 -->

<!-- BLOCK_5 | doxlgeVvP2TLspFKt7i8G7nUcsf -->
<callout icon="gift" bgc="7" bc="1">
Claude Code 构建了一套**企业级用户追踪和行为分析系统**，通过 Device ID（永久标识）+ 环境指纹（40+ 维度）+ 行为遥测（640+ 事件类型）形成完整的用户画像。封号并非单一触发，而是多维度综合判定。
</callout>
<!-- END_BLOCK_5 -->

<!-- BLOCK_6 | doxlg0HxRhlCOSKJroMdVkpOJQb -->
### 封号最可能的 5 大原因<!-- 标题序号: 1.1.1 --><!-- END_BLOCK_6 -->

<!-- BLOCK_7 | doxlgK8b9tvymNQEFXoZe5LhzKh -->
<table header-row="true" col-widths="80,164,80,378">
    <tr>
        <td>排名</td>
        <td>原因</td>
        <td>风险等级</td>
        <td>说明</td>
    </tr>
    <tr>
        <td>1</td>
        <td>订阅滥用/共享账号</td>
        <td>极高</td>
        <td>Device ID 跨设备关联，检测多设备共享同一账号</td>
    </tr>
    <tr>
        <td>2</td>
        <td>速率限制违规</td>
        <td>高</td>
        <td>超出 rateLimitTier 配额，短时间高频调用</td>
    </tr>
    <tr>
        <td>3</td>
        <td>内容策略违规</td>
        <td>高</td>
        <td>消息内容指纹 + anti-distillation 检测</td>
    </tr>
    <tr>
        <td>4</td>
        <td>自动化滥用</td>
        <td>中</td>
        <td>CI/CD 环境检测 + 非交互模式识别 + 异常 token 消耗</td>
    </tr>
    <tr>
        <td>5</td>
        <td>使用非官方客户端/篡改</td>
        <td>中</td>
        <td>指纹校验失败 + 版本归因 header 异常</td>
    </tr>
</table>
<!-- END_BLOCK_7 -->

<!-- BLOCK_8 | doxlgeWKh16pRAFxXGsR4Y7FWQd -->
---
<!-- END_BLOCK_8 -->

<!-- BLOCK_9 | doxlgSBRuEHM4G1DTviNqkgjmgf -->
## 二、身份追踪体系<!-- 标题序号: 1.2 --><!-- END_BLOCK_9 -->

<!-- BLOCK_10 | doxlgyKr5fHxhUwXAKrp7hjmvCv -->
### 2.1 持久化标识符<!-- END_BLOCK_10 -->

<!-- BLOCK_11 | doxlgIXbvafuZfrlbdddfpm9Cye -->
Claude Code 通过以下标识符**跨会话永久追踪用户**：
<!-- END_BLOCK_11 -->

<!-- BLOCK_12 | doxlgL0ey6YPTLi8XfiVnlYar1g -->
<table header-row="true" col-widths="152,315,257,168">
    <tr>
        <td>标识符</td>
        <td>生成方式</td>
        <td>存储位置</td>
        <td>生命周期</td>
    </tr>
    <tr>
        <td>Device ID</td>
        <td>`randomBytes(32).toString('hex')`</td>
        <td>`~/.claude/config.json`</td>
        <td>**永久**，除非手动删除</td>
    </tr>
    <tr>
        <td>Account UUID</td>
        <td>OAuth 登录时服务端下发</td>
        <td>`~/.claude/config.json`</td>
        <td>绑定账号</td>
    </tr>
    <tr>
        <td>Organization UUID</td>
        <td>OAuth 登录时下发</td>
        <td>同上</td>
        <td>绑定组织</td>
    </tr>
    <tr>
        <td>Session ID</td>
        <td>`randomUUID()` 每次会话生成</td>
        <td>内存</td>
        <td>单次会话</td>
    </tr>
    <tr>
        <td>Email</td>
        <td>OAuth 或 `git config user.email`</td>
        <td>同上</td>
        <td>绑定身份</td>
    </tr>
</table>
<!-- END_BLOCK_12 -->

<!-- BLOCK_13 | doxlgN3eq7juu2NWLODLdZQhf3b -->
<callout icon="gift" bgc="2" bc="1">
Device ID 是 256 位随机值，首次运行时生成并**永久存储**。它是所有事件上报的核心标识，Anthropic 可通过它精确关联同一设备上的所有活动。
</callout>
<!-- END_BLOCK_13 -->

<!-- BLOCK_14 | doxlgwf17J5NhXzEgwfMt29fQHb -->
### 2.2 消息内容指纹（Fingerprint）<!-- END_BLOCK_14 -->

<!-- BLOCK_15 | doxlg3uQyE7kIDUPI2r5f7C5INg -->
源码位置：`src/utils/fingerprint.ts`
<!-- END_BLOCK_15 -->

<!-- BLOCK_16 | doxlglFeMgH34VZB1OIykFGNjzh -->
```
算法：SHA256( "59cf53e54c78" + 首条消息[4] + 首条消息[7] + 首条消息[20] + 版本号 )[:3]
```
<!-- END_BLOCK_16 -->

<!-- BLOCK_17 | doxlgeJQu2LMudQke5SWc7D9uuf -->
这个 3 字符指纹被嵌入到：
<!-- END_BLOCK_17 -->

<!-- BLOCK_18 | doxlg6Gam0GLXqlvNWrumX8jNRb -->
- HTTP Header `x-anthropic-billing-header` 的 `cc_version` 字段
<!-- END_BLOCK_18 -->

<!-- BLOCK_19 | doxlgsBP7VJPEp52uRt9viePTWe -->
- System Prompt 中
<!-- END_BLOCK_19 -->

<!-- BLOCK_20 | doxlgT9QbdxOsJcFnQKXRIT4zah -->
**用途推测**：后端可通过此指纹**校验请求来源合法性**，检测是否有人使用非官方客户端伪造请求。
<!-- END_BLOCK_20 -->

<!-- BLOCK_21 | doxlgaHL5Mfrfgg81WByWKoU1oe -->
### 2.3 每次 API 请求携带的身份信息<!-- END_BLOCK_21 -->

<!-- BLOCK_22 | doxlgkn5nheSE8mnS2wb1dvpAP6 -->
```
HTTP Headers:
  x-app: cli
  User-Agent: claude-cli/2.x.x (external, cli)
  X-Claude-Code-Session-Id: {SESSION_UUID}
  x-anthropic-billing-header: cc_version=2.x.x.{FINGERPRINT}; cc_entrypoint=cli
  x-client-request-id: {REQUEST_UUID}

Request Body metadata:
  user_id: JSON 编码的 Device ID + Account UUID + Session ID
```
<!-- END_BLOCK_22 -->

<!-- BLOCK_23 | doxlgGwfxPZUM6RF526OBcihSil -->
---
<!-- END_BLOCK_23 -->

<!-- BLOCK_24 | doxlg1BB4ESiwbaTUszSWv2yMeb -->
## 三、环境指纹采集（40+ 维度）<!-- 标题序号: 1.3 --><!-- END_BLOCK_24 -->

<!-- BLOCK_25 | doxlglDcpf7LnwCehFoFsIuEgkc -->
<callout icon="gift" bgc="3" bc="1">
Claude Code 在启动时对用户环境进行**大规模枚举**，以下信息全部上报至服务端。
</callout>
<!-- END_BLOCK_25 -->

<!-- BLOCK_26 | doxlgzSJ3EJeg7QfQoMxJweCZzb -->
### 3.1 系统级信息<!-- END_BLOCK_26 -->

<!-- BLOCK_27 | doxlggADxGzZX3oD2py3SMDCmRg -->
<table header-row="true" col-widths="200,248,252">
    <tr>
        <td>采集维度</td>
        <td>数据来源</td>
        <td>示例值</td>
    </tr>
    <tr>
        <td>操作系统</td>
        <td>`process.platform`</td>
        <td>darwin/linux/win32</td>
    </tr>
    <tr>
        <td>CPU 架构</td>
        <td>`process.arch`</td>
        <td>x64/arm64</td>
    </tr>
    <tr>
        <td>Node.js 版本</td>
        <td>`process.version`</td>
        <td>v20.x.x</td>
    </tr>
    <tr>
        <td>Linux 发行版</td>
        <td>`/etc/os-release`</td>
        <td>ubuntu 22.04</td>
    </tr>
    <tr>
        <td>Linux 内核</td>
        <td>`os.release()`</td>
        <td>6.5.0-xxx</td>
    </tr>
    <tr>
        <td>WSL 版本</td>
        <td>`/proc/version` 解析</td>
        <td>WSL2</td>
    </tr>
</table>
<!-- END_BLOCK_27 -->

<!-- BLOCK_28 | doxlgMQyvuDamckFw6W75tIkv9e -->
### 3.2 运行环境检测（40+ 种）<!-- END_BLOCK_28 -->

<!-- BLOCK_29 | doxlgDduQRFDLjJv7avNPMPkpeb -->
Claude Code 通过环境变量和文件探测自动识别运行环境：
<!-- END_BLOCK_29 -->

<!-- BLOCK_30 | doxlgHNprWNUXxd2jW1ESu3LDJh -->
**CI/CD 平台**：
<!-- END_BLOCK_30 -->

<!-- BLOCK_31 | doxlgVXfdQqubQgisWqbkwm44Nc -->
- GitHub Actions（`GITHUB_ACTIONS`）
<!-- END_BLOCK_31 -->

<!-- BLOCK_32 | doxlgXPOGgsiIB5E4Rn84VOMaMg -->
- GitLab CI（`GITLAB_CI`）
<!-- END_BLOCK_32 -->

<!-- BLOCK_33 | doxlg2PF7wHb7lRGGE8mtKhOgef -->
- CircleCI（`CIRCLECI`）
<!-- END_BLOCK_33 -->

<!-- BLOCK_34 | doxlgWavFpO31tH3T1uviKPXwkg -->
- Buildkite（`BUILDKITE`）
<!-- END_BLOCK_34 -->

<!-- BLOCK_35 | doxlgaWR8T4bzGsO7VhNI6sTmjh -->
- Jenkins（`JENKINS_URL`）
<!-- END_BLOCK_35 -->

<!-- BLOCK_36 | doxlgj72H7HCZvPwrwD2HxDbiRd -->
**云开发环境**：
<!-- END_BLOCK_36 -->

<!-- BLOCK_37 | doxlgUtTSPDTiQozkRSSu2oUceh -->
- GitHub Codespaces（`CODESPACES`）
<!-- END_BLOCK_37 -->

<!-- BLOCK_38 | doxlgOZY8s7U1EXjP6TbxePbQ5b -->
- Gitpod（`GITPOD_WORKSPACE_ID`）
<!-- END_BLOCK_38 -->

<!-- BLOCK_39 | doxlgWJXvSJOAwjALQDIv1ZppHf -->
- Replit（`REPL_ID`）
<!-- END_BLOCK_39 -->

<!-- BLOCK_40 | doxlgAy15dcCMtUs2LTtvwFhc1f -->
- Vercel（`VERCEL`）
<!-- END_BLOCK_40 -->

<!-- BLOCK_41 | doxlgwbbalZbtX6q9DBweZLpKEd -->
- Railway（`RAILWAY_ENVIRONMENT`）
<!-- END_BLOCK_41 -->

<!-- BLOCK_42 | doxlgfVmy3yyJz7DmxCNyvE7aZb -->
**云平台**：
<!-- END_BLOCK_42 -->

<!-- BLOCK_43 | doxlgqEBCU0DNuIunUpxfBPdD0b -->
- AWS Lambda/Fargate/ECS/EC2
<!-- END_BLOCK_43 -->

<!-- BLOCK_44 | doxlgbdAdJehaOHiMFrUMQsCMKg -->
- GCP Cloud Run
<!-- END_BLOCK_44 -->

<!-- BLOCK_45 | doxlgGycxnrX17qh4GgWgdEoxth -->
- Azure Functions/App Service
<!-- END_BLOCK_45 -->

<!-- BLOCK_46 | doxlgZFQ4vboV9DObrxVlVCAArb -->
**容器和虚拟化**：
<!-- END_BLOCK_46 -->

<!-- BLOCK_47 | doxlgN1eJ49LVlS3LMu1TdliFSb -->
- Docker（检测 `/.dockerenv` 文件）
<!-- END_BLOCK_47 -->

<!-- BLOCK_48 | doxlgBdc6tefxPrM8EwTTwCASlf -->
- Kubernetes（`KUBERNETES_SERVICE_HOST`）
<!-- END_BLOCK_48 -->

<!-- BLOCK_49 | doxlg73QGmKxdcKkLpCSyRTLDMc -->
- WSL（`WSL_DISTRO_NAME`）
<!-- END_BLOCK_49 -->

<!-- BLOCK_50 | doxlgUB8Or2VEdpzDsLpq8vboGg -->
**终端和 IDE**：
<!-- END_BLOCK_50 -->

<!-- BLOCK_51 | doxlg6PrErrPgLUPTfhSaruTR2d -->
- VSCode（`VSCODE_*`）
<!-- END_BLOCK_51 -->

<!-- BLOCK_52 | doxlgv9qhntAhjNCygB6oD7Vb5b -->
- Cursor（`CURSOR_TRACE_ID`）
<!-- END_BLOCK_52 -->

<!-- BLOCK_53 | doxlgE4Swf1kAZh47vkk5wNliXd -->
- JetBrains（`TERMINAL_EMULATOR=JetBrains-JediTerm`）
<!-- END_BLOCK_53 -->

<!-- BLOCK_54 | doxlgKnoAxugzKKkYaPKzeONhkh -->
- tmux/screen/SSH
<!-- END_BLOCK_54 -->

<!-- BLOCK_55 | doxlgzGqnqEoV7E3Laqqd1z0Paf -->
### 3.3 开发工具链<!-- END_BLOCK_55 -->

<!-- BLOCK_56 | doxlgMNGqAmTh70mJO8U1RBfukb -->
<table header-row="true" col-widths="152,288,260">
    <tr>
        <td>检测类型</td>
        <td>方法</td>
        <td>采集内容</td>
    </tr>
    <tr>
        <td>包管理器</td>
        <td>`which npm/yarn/pnpm`</td>
        <td>可用的包管理器列表</td>
    </tr>
    <tr>
        <td>运行时</td>
        <td>`which bun/deno/node`</td>
        <td>可用的运行时列表</td>
    </tr>
    <tr>
        <td>VCS</td>
        <td>检测 `.git/.hg/.svn` 等</td>
        <td>版本控制系统类型</td>
    </tr>
    <tr>
        <td>Git 仓库</td>
        <td>`git remote get-url origin`</td>
        <td>SHA256 哈希取前 16 字符</td>
    </tr>
</table>
<!-- END_BLOCK_56 -->

<!-- BLOCK_57 | doxlgyeF2vxjeWmtKOba9nxExxc -->
### 3.4 GitHub Actions 专项采集<!-- END_BLOCK_57 -->

<!-- BLOCK_58 | doxlgiaz1JNtbhcjADhj2c16q9d -->
在 CI 环境中，额外采集：
<!-- END_BLOCK_58 -->

<!-- BLOCK_59 | doxlgr2iRmmYvhit3T6XwzKtAqh -->
<table header-row="true" col-widths="398,302">
    <tr>
        <td>字段</td>
        <td>内容</td>
    </tr>
    <tr>
        <td>`GITHUB_ACTOR_ID`</td>
        <td>触发者 ID</td>
    </tr>
    <tr>
        <td>`GITHUB_REPOSITORY_ID`</td>
        <td>仓库 ID</td>
    </tr>
    <tr>
        <td>`GITHUB_REPOSITORY_OWNER_ID`</td>
        <td>仓库所有者 ID</td>
    </tr>
    <tr>
        <td>`GITHUB_EVENT_NAME`</td>
        <td>事件类型</td>
    </tr>
    <tr>
        <td>`RUNNER_ENVIRONMENT`</td>
        <td>Runner 环境</td>
    </tr>
    <tr>
        <td>`RUNNER_OS`</td>
        <td>Runner 操作系统</td>
    </tr>
</table>
<!-- END_BLOCK_59 -->

<!-- BLOCK_60 | doxlgPcx4Wy97oXjMzYjsQo9LDc -->
---
<!-- END_BLOCK_60 -->

<!-- BLOCK_61 | doxlguQZp4ugUNJAyKM2FI5KkLe -->
## 四、遥测上报系统<!-- 标题序号: 1.4 --><!-- END_BLOCK_61 -->

<!-- BLOCK_62 | doxlgEj0Iu6yGnWHyfUd0Pocr5L -->
### 4.1 三路并发上报架构<!-- END_BLOCK_62 -->

<!-- BLOCK_63 | doxlg3vC05ebNnz4bUhI3TuULVb -->
![board_GvJpwgtmUhFcDobzBMcl62KRgzh](board_GvJpwgtmUhFcDobzBMcl62KRgzh.drawio)
<!-- END_BLOCK_63 -->

<!-- BLOCK_64 | doxlgqOVyt8wvNb9B977k31MzGe -->
### 4.2 第一方事件日志（最核心）<!-- END_BLOCK_64 -->

<!-- BLOCK_65 | doxlgiLYcPjNu6UTmeERt0mPEOh -->
**上报端点**：`https://api.anthropic.com/api/event_logging/batch`
<!-- END_BLOCK_65 -->

<!-- BLOCK_66 | doxlghHA1j8Lu4axdehu7z0vsGc -->
**上报频率**：每 5 秒批量发送，最多 200 条/批
<!-- END_BLOCK_66 -->

<!-- BLOCK_67 | doxlgMd3ZhchENIWHTjh6ZlV4mf -->
**失败恢复**：上报失败的事件持久化到 `~/.claude/telemetry/` 目录，下次启动自动重试（最多 8 次，指数退避）
<!-- END_BLOCK_67 -->

<!-- BLOCK_68 | doxlgbJUVbRg7FmQpBXzMfoVO8f -->
**关键事件类型**（640+ 种中最核心的）：
<!-- END_BLOCK_68 -->

<!-- BLOCK_69 | doxlg5qJWegowzxwrQxXGjzbc7f -->
<table header-row="true" col-widths="186,340,172">
    <tr>
        <td>事件名</td>
        <td>上报内容</td>
        <td>封号相关性</td>
    </tr>
    <tr>
        <td>`tengu_init`</td>
        <td>完整环境指纹、设备信息</td>
        <td>高 - 环境异常检测</td>
    </tr>
    <tr>
        <td>`tengu_api_success`</td>
        <td>模型名、token 用量、耗时、成本美元、TTFT</td>
        <td>极高 - 用量监控</td>
    </tr>
    <tr>
        <td>`tengu_api_error`</td>
        <td>错误类型、HTTP 状态码、重试次数</td>
        <td>中 - 异常模式</td>
    </tr>
    <tr>
        <td>`tengu_tool_use_*`</td>
        <td>工具名、执行耗时、成功/失败</td>
        <td>中 - 行为模式</td>
    </tr>
    <tr>
        <td>`tengu_exit`</td>
        <td>会话时长、总 token 用量</td>
        <td>高 - 会话级统计</td>
    </tr>
    <tr>
        <td>`tengu_oauth_*`</td>
        <td>登录/刷新/失败事件</td>
        <td>高 - 账号安全</td>
    </tr>
    <tr>
        <td>`tengu_cancel`</td>
        <td>取消操作</td>
        <td>低</td>
    </tr>
    <tr>
        <td>`tengu_auto_mode_*`</td>
        <td>自动模式使用</td>
        <td>中 - 自动化检测</td>
    </tr>
</table>
<!-- END_BLOCK_69 -->

<!-- BLOCK_70 | doxlg6MzcuAhNPW1Wqc0wkJb6re -->
### 4.3 每个事件附带的元数据<!-- END_BLOCK_70 -->

<!-- BLOCK_71 | doxlgsyISWPtu01f4E3QSlZT36c -->
```
核心字段:
  session_id          - 会话 ID
  device_id           - 设备 ID（永久）
  account_uuid        - 账户 UUID
  organization_uuid   - 组织 UUID
  subscription_type   - 订阅类型（pro/max/enterprise/team）
  rate_limit_tier     - 速率限制等级
  model               - 使用的模型
  version             - 客户端版本
  platform            - 操作系统
  arch                - CPU 架构
  entrypoint          - 入口点（cli/sdk/bridge）
  is_interactive      - 是否交互模式
  is_ci               - 是否 CI 环境

环境指纹（env 对象）:
  terminal            - 终端类型
  package_managers     - 包管理器
  runtimes            - 运行时
  is_docker           - Docker 环境
  deployment_env      - 部署环境类型
  linux_distro_*      - Linux 发行版信息

进程资源（Base64 编码）:
  uptime              - 进程运行时间
  rss                 - 内存占用
  heapUsed            - 堆内存使用
  cpuPercent          - CPU 使用率
```
<!-- END_BLOCK_71 -->

<!-- BLOCK_72 | doxlgMc7r4wZJcgRA7f6KMIsyfe -->
### 4.4 Datadog 上报<!-- END_BLOCK_72 -->

<!-- BLOCK_73 | doxlgJYIOHO57i9RzB1gRAZhx1V -->
**端点**：`https://http-intake.logs.us5.datadoghq.com/api/v2/logs`
<!-- END_BLOCK_73 -->

<!-- BLOCK_74 | doxlgbVwAZKxeXEov96E8I8XdLe -->
**硬编码客户端 Token**：`pubbbf48e6d78dae54bceaa4acf463299bf`
<!-- END_BLOCK_74 -->

<!-- BLOCK_75 | doxlgCh31VUgvWM2yV0RiYuOJLc -->
**白名单**：64 种事件类型
<!-- END_BLOCK_75 -->

<!-- BLOCK_76 | doxlgxrD89kZ6IuzxjkceDuEB3c -->
**标签化字段**（用于后台聚合分析和告警）：
<!-- END_BLOCK_76 -->

<!-- BLOCK_77 | doxlgYk94ja8mvcr6pzEQeBKize -->
<table header-row="true" col-widths="200,318,180">
    <tr>
        <td>标签</td>
        <td>说明</td>
        <td>封号相关性</td>
    </tr>
    <tr>
        <td>`userBucket`</td>
        <td>用户分组（基于 ID 哈希到 30 个桶）</td>
        <td>高 - 用户分群</td>
    </tr>
    <tr>
        <td>`subscriptionType`</td>
        <td>订阅类型</td>
        <td>极高 - 配额管理</td>
    </tr>
    <tr>
        <td>`model`</td>
        <td>使用模型</td>
        <td>高 - 资源消耗</td>
    </tr>
    <tr>
        <td>`toolName`</td>
        <td>工具名称</td>
        <td>中 - 行为模式</td>
    </tr>
    <tr>
        <td>`platform`</td>
        <td>操作系统</td>
        <td>低</td>
    </tr>
    <tr>
        <td>`provider`</td>
        <td>API 提供商</td>
        <td>中</td>
    </tr>
    <tr>
        <td>`version`</td>
        <td>客户端版本</td>
        <td>中 - 旧版本检测</td>
    </tr>
</table>
<!-- END_BLOCK_77 -->

<!-- BLOCK_78 | doxlgVJ7oReLrADSRK5WzsQcG4g -->
### 4.5 GrowthBook 双向数据交换<!-- END_BLOCK_78 -->

<!-- BLOCK_79 | doxlgZ7uQ0uLdx2vejD5SUrDkBc -->
**SDK Key**：`sdk-zAZezfDKGoZuXXKe`（外部用户）
<!-- END_BLOCK_79 -->

<!-- BLOCK_80 | doxlgr3GVRWYdKjivdau6JmQ9vf -->
**发送给 GrowthBook 的用户属性**：
<!-- END_BLOCK_80 -->

<!-- BLOCK_81 | doxlgBZTR7mVTJRff18zF0OAyAe -->
<table header-row="true" col-widths="365,335">
    <tr>
        <td>属性</td>
        <td>说明</td>
    </tr>
    <tr>
        <td>`id`</td>
        <td>Device ID</td>
    </tr>
    <tr>
        <td>`sessionId`</td>
        <td>会话 ID</td>
    </tr>
    <tr>
        <td>`platform`</td>
        <td>操作系统</td>
    </tr>
    <tr>
        <td>`organizationUUID`</td>
        <td>组织 ID</td>
    </tr>
    <tr>
        <td>`accountUUID`</td>
        <td>账户 ID</td>
    </tr>
    <tr>
        <td>`subscriptionType`</td>
        <td>订阅类型</td>
    </tr>
    <tr>
        <td>`rateLimitTier`</td>
        <td>速率限制等级</td>
    </tr>
    <tr>
        <td>`firstTokenTime`</td>
        <td>首次使用时间戳</td>
    </tr>
</table>
<!-- END_BLOCK_81 -->

<!-- BLOCK_82 | doxlgTtSVlDSyLMFWTPU2g7osgc -->
<table header-row="true" col-widths="365,335">
    <tr>
        <td>属性</td>
        <td>说明</td>
    </tr>
    <tr>
        <td>`email`</td>
        <td>用户邮箱</td>
    </tr>
    <tr>
        <td>`appVersion`</td>
        <td>客户端版本</td>
    </tr>
</table>
<!-- END_BLOCK_82 -->

<!-- BLOCK_83 | doxlgzcFNqu1B7885xQ10BHJahf -->
**作用**：服务端可据此**精确控制功能开关**、A/B 实验分配，甚至**针对特定用户禁用功能**。
<!-- END_BLOCK_83 -->

<!-- BLOCK_84 | doxlgaHLCpmHl7YRRpPcGyhXmpe -->
---
<!-- END_BLOCK_84 -->

<!-- BLOCK_85 | doxlg4yfpDtDMiqDmtoucuseAUd -->
## 五、服务端远程控制机制<!-- 标题序号: 1.5 --><!-- END_BLOCK_85 -->

<!-- BLOCK_86 | doxlgLR7Oi4wiVUmPud9vo9AOVe -->
<callout icon="gift" bgc="2" bc="1">
Anthropic 可通过以下机制**远程控制客户端行为**，无需用户知情。
</callout>
<!-- END_BLOCK_86 -->

<!-- BLOCK_87 | doxlgJh6E17pOK92luMoqIAHnHh -->
### 5.1 Policy Limits（组织级策略限制）<!-- END_BLOCK_87 -->

<!-- BLOCK_88 | doxlgF5YNvvWAZqJs43IE4gquob -->
**端点**：`/api/claude_code/policy_limits`
<!-- END_BLOCK_88 -->

<!-- BLOCK_89 | doxlgFQpGJRbCO7WAYWOXkA9jmg -->
**轮询频率**：每小时
<!-- END_BLOCK_89 -->

<!-- BLOCK_90 | doxlgSkYAfQBFRtFyLAkkXxEJCe -->
**能力**：
<!-- END_BLOCK_90 -->

<!-- BLOCK_91 | doxlg1hyOGDyWcoO2qoWxf2oYSd -->
- 禁用特定工具
<!-- END_BLOCK_91 -->

<!-- BLOCK_92 | doxlg1TJqorHQ9SgWWqaxN7dNOf -->
- 限制功能访问
<!-- END_BLOCK_92 -->

<!-- BLOCK_93 | doxlgD9t9aXUmxjLgSNI5h0V8Cc -->
- 强制执行组织安全策略
<!-- END_BLOCK_93 -->

<!-- BLOCK_94 | doxlgf6UVhS4JY46NbS6X3fzMLU -->
### 5.2 Remote Managed Settings（远程设置推送）<!-- END_BLOCK_94 -->

<!-- BLOCK_95 | doxlgROuAFUCygWOanKZiY1kYZf -->
**端点**：`/api/claude_code/settings`
<!-- END_BLOCK_95 -->

<!-- BLOCK_96 | doxlg4Wef5pYRf6x6hYVOcMIzQg -->
**能力**：
<!-- END_BLOCK_96 -->

<!-- BLOCK_97 | doxlgrs4Upskj0eon2Gtl4Q6u5b -->
- 远程推送 `settings.json` 配置
<!-- END_BLOCK_97 -->

<!-- BLOCK_98 | doxlgAQRerLtwXgpCYhm1Sc3bXc -->
- 修改客户端行为
<!-- END_BLOCK_98 -->

<!-- BLOCK_99 | doxlgLZGsMzvHZPsFRCGMLmTtGh -->
- 企业治理
<!-- END_BLOCK_99 -->

<!-- BLOCK_100 | doxlgrMMmBf83wTYUgXkgEXxhCp -->
### 5.3 版本强制升级<!-- END_BLOCK_100 -->

<!-- BLOCK_101 | doxlgc6FoGUXSNazNal7r8Hefqg -->
通过 GrowthBook 配置 `tengu_version_config` 下发**最低版本要求**，可强制旧版本用户升级。
<!-- END_BLOCK_101 -->

<!-- BLOCK_102 | doxlgUVGgVt2nyjPpNCCKem0Kmf -->
### 5.4 功能标志灰度控制<!-- END_BLOCK_102 -->

<!-- BLOCK_103 | doxlgZlS98Y8guVzETSY0ia7iBD -->
服务端可通过 GrowthBook 对特定用户/组织：
<!-- END_BLOCK_103 -->

<!-- BLOCK_104 | doxlgUAFJG6oH0FZ9JgpzpWvMdc -->
- 关闭特定功能
<!-- END_BLOCK_104 -->

<!-- BLOCK_105 | doxlg2rlG8htyTQuC2cypSZsMdh -->
- 调整速率限制
<!-- END_BLOCK_105 -->

<!-- BLOCK_106 | doxlgWxtT61fHIDBDLwNN8JMjPe -->
- 修改采样率
<!-- END_BLOCK_106 -->

<!-- BLOCK_107 | doxlgIOK0kq06DUgMbD3hQSxLsc -->
- 启用/禁用 Beta 特性
<!-- END_BLOCK_107 -->

<!-- BLOCK_108 | doxlg9I0ko46C1LliBQpOsWuHte -->
---
<!-- END_BLOCK_108 -->

<!-- BLOCK_109 | doxlgOkisYU0I1B2a0umVWxPwPg -->
## 六、封号触发机制深度分析<!-- 标题序号: 1.6 --><!-- END_BLOCK_109 -->

<!-- BLOCK_110 | doxlgZKHOe1fGhQ5w6HAyxQuOrd -->
### 6.1 身份关联检测（账号共享）<!-- END_BLOCK_110 -->

<!-- BLOCK_111 | doxlgg0hzXa9OblQKU5eTVtgEbe -->
![board_VneSwuBqUhs5yPbRSgkleAGtgOd](board_VneSwuBqUhs5yPbRSgkleAGtgOd.drawio)
<!-- END_BLOCK_111 -->

<!-- BLOCK_112 | doxlgFzgBmjb9OKAvVk0qttvE0g -->
**检测依据**：
<!-- END_BLOCK_112 -->

<!-- BLOCK_113 | doxlggmFFBxYpRXj9t4amhM9iZf -->
- 同一 Account UUID 出现在多个 Device ID 上
<!-- END_BLOCK_113 -->

<!-- BLOCK_114 | doxlgrjGZUbcvYMuL8zWJUjgYvO -->
- 不同 IP、不同操作系统、不同时区的设备使用同一账号
<!-- END_BLOCK_114 -->

<!-- BLOCK_115 | doxlgrDRrNAck1rXDa6zic7BPvf -->
- 短时间内在不同地理位置登录
<!-- END_BLOCK_115 -->

<!-- BLOCK_116 | doxlgLtGj6mJ52NnVjjGyW3OtWe -->
### 6.2 速率限制违规<!-- END_BLOCK_116 -->

<!-- BLOCK_117 | doxlgGIvLlGIFn1aNtig4sEpokd -->
**检测链路**：
<!-- END_BLOCK_117 -->

<!-- BLOCK_118 | doxlgVdhYYrhC5f1c6ReYj7VEnh -->
1. 每次 API 请求上报 `tengu_api_success`，包含 token 用量和模型
<!-- END_BLOCK_118 -->

<!-- BLOCK_119 | doxlgicv21ugnMUWOqZkxfXmbng -->
2. 服务端按 `account_uuid` + `subscription_type` + `rate_limit_tier` 聚合
<!-- END_BLOCK_119 -->

<!-- BLOCK_120 | doxlgNDxVlAvBiulEwwcfAQwZUg -->
3. 超出配额阈值 → 429 错误 → 继续违规 → 触发封号
<!-- END_BLOCK_120 -->

<!-- BLOCK_121 | doxlgB1zD0q9A0wvKpQYoRRgTcd -->
**上报的关键指标**：
<!-- END_BLOCK_121 -->

<!-- BLOCK_122 | doxlgA5aSkDtMuKFeBHraVBuJ98 -->
<table header-row="true" col-widths="363,337">
    <tr>
        <td>指标</td>
        <td>说明</td>
    </tr>
    <tr>
        <td>`inputTokens`</td>
        <td>输入 token 数</td>
    </tr>
    <tr>
        <td>`outputTokens`</td>
        <td>输出 token 数</td>
    </tr>
    <tr>
        <td>`cacheReadTokens`</td>
        <td>缓存读取 token</td>
    </tr>
    <tr>
        <td>`cacheCreationTokens`</td>
        <td>缓存创建 token</td>
    </tr>
    <tr>
        <td>`costUsd`</td>
        <td>每次请求的美元成本</td>
    </tr>
    <tr>
        <td>`duration`</td>
        <td>请求耗时</td>
    </tr>
    <tr>
        <td>`model`</td>
        <td>使用的模型</td>
    </tr>
</table>
<!-- END_BLOCK_122 -->

<!-- BLOCK_123 | doxlgDwFGAWsRw6LUoVch0LcmWf -->
### 6.3 内容策略违规<!-- END_BLOCK_123 -->

<!-- BLOCK_124 | doxlgLRJL22FFgjBPNKquHzzKKh -->
**防线 1 - Anti-Distillation**：
<!-- END_BLOCK_124 -->

<!-- BLOCK_125 | doxlgHQ96ElQOUXUAPGKcnloKfc -->
API 请求中包含 `anti_distillation: ["fake_tools"]`，检测是否有人用 Claude Code 输出训练竞争模型。
<!-- END_BLOCK_125 -->

<!-- BLOCK_126 | doxlg7KCkzCyouIZjj8u2Ffxtsf -->
**防线 2 - 消息指纹**：
<!-- END_BLOCK_126 -->

<!-- BLOCK_127 | doxlg2rvULEaK7M6hNxU3johbjc -->
从首条消息特定位置提取字符计算指纹，嵌入 Header 和 System Prompt，后端可校验请求完整性。
<!-- END_BLOCK_127 -->

<!-- BLOCK_128 | doxlgQDiVr4hyKiKrdM2jDuH47b -->
**防线 3 - 额外保护标志**：
<!-- END_BLOCK_128 -->

<!-- BLOCK_129 | doxlgBV5yJTSvzxBC50stUjwQWb -->
`x-anthropic-additional-protection: true` header 可触发服务端更严格的内容审查。
<!-- END_BLOCK_129 -->

<!-- BLOCK_130 | doxlgXFcXNeVy0PGoQWqfP9s6SG -->
### 6.4 自动化滥用检测<!-- END_BLOCK_130 -->

<!-- BLOCK_131 | doxlgFmO1wIAPAx9l4p5bjckLGg -->
<table header-row="true" col-widths="261,197,241">
    <tr>
        <td>检测信号</td>
        <td>来源</td>
        <td>说明</td>
    </tr>
    <tr>
        <td>`is_ci: true`</td>
        <td>环境变量 `CI`</td>
        <td>标记为 CI 环境</td>
    </tr>
    <tr>
        <td>`is_github_action: true`</td>
        <td>`GITHUB_ACTIONS`</td>
        <td>GitHub Actions 运行</td>
    </tr>
    <tr>
        <td>`is_interactive: false`</td>
        <td>TTY 检测</td>
        <td>非交互式调用</td>
    </tr>
    <tr>
        <td>`entrypoint: "sdk"`</td>
        <td>入口点</td>
        <td>通过 SDK 调用而非 CLI</td>
    </tr>
    <tr>
        <td>`auto_mode_*` 事件</td>
        <td>自动模式</td>
        <td>AFK 自动执行模式</td>
    </tr>
</table>
<!-- END_BLOCK_131 -->

<!-- BLOCK_132 | doxlgWXEeOfaABfsnhwQueMaMGh -->
**风险组合**：非交互 + SDK 入口 + 高频调用 + 大量 token 消耗 → 自动化滥用嫌疑
<!-- END_BLOCK_132 -->

<!-- BLOCK_133 | doxlgnn6CRCi0apt7vBpbpACfbe -->
### 6.5 客户端篡改检测<!-- END_BLOCK_133 -->

<!-- BLOCK_134 | doxlga1clg7TMnBahKtx9N4Z3cb -->
<table header-row="true" col-widths="152,288,260">
    <tr>
        <td>检测点</td>
        <td>方法</td>
        <td>后果</td>
    </tr>
    <tr>
        <td>版本指纹</td>
        <td>`cc_version={VER}.{FINGERPRINT}`</td>
        <td>指纹不匹配 → 异常标记</td>
    </tr>
    <tr>
        <td>User-Agent</td>
        <td>格式校验</td>
        <td>非标准格式 → 可能非官方客户端</td>
    </tr>
    <tr>
        <td>Beta Headers</td>
        <td>预期 header 集合</td>
        <td>缺失/多余 → 异常</td>
    </tr>
    <tr>
        <td>System Prompt</td>
        <td>包含归因信息</td>
        <td>篡改 → 检测到</td>
    </tr>
</table>
<!-- END_BLOCK_134 -->

<!-- BLOCK_135 | doxlgyv00c7ZypTngcBALLpMytb -->
---
<!-- END_BLOCK_135 -->

<!-- BLOCK_136 | doxlgzNXBHVGahYTnHxEEc828Je -->
## 七、完整数据流向全景图<!-- 标题序号: 1.7 --><!-- END_BLOCK_136 -->

<!-- BLOCK_137 | doxlgbg0C9kamgJFlRFeZELdW5c -->
![board_NmbxwEmjDhgXB0bs2MalE3Usg6f](board_NmbxwEmjDhgXB0bs2MalE3Usg6f.drawio)
<!-- END_BLOCK_137 -->

<!-- BLOCK_138 | doxlgVrwqkgWG38V8e7fKo8ilsc -->
---
<!-- END_BLOCK_138 -->

<!-- BLOCK_139 | doxlg9twwVXRXINLUze6jiK86hb -->
## 八、所有外部通信端点汇总<!-- 标题序号: 1.8 --><!-- END_BLOCK_139 -->

<!-- BLOCK_140 | doxlgCAnnrwlgZUc1smi1qyWzNh -->
<table header-row="true" col-widths="140,450,102,190,80">
    <tr>
        <td>目标</td>
        <td>URL</td>
        <td>频率</td>
        <td>数据内容</td>
        <td>可禁用</td>
    </tr>
    <tr>
        <td>主 API</td>
        <td>`api.anthropic.com`</td>
        <td>每次对话</td>
        <td>对话内容 + 身份 + 元数据</td>
        <td>不可</td>
    </tr>
    <tr>
        <td>1P 事件</td>
        <td>`api.anthropic.com/api/event_logging/batch`</td>
        <td>每 5 秒</td>
        <td>640+ 事件 + 环境指纹</td>
        <td>可</td>
    </tr>
    <tr>
        <td>Datadog</td>
        <td>`datadoghq.com`</td>
        <td>每 15 秒</td>
        <td>64 种事件 + 标签</td>
        <td>可</td>
    </tr>
    <tr>
        <td>GrowthBook</td>
        <td>`api.anthropic.com`</td>
        <td>每 20 分钟</td>
        <td>用户属性换功能配置</td>
        <td>可</td>
    </tr>
    <tr>
        <td>OAuth</td>
        <td>`platform.claude.com`</td>
        <td>登录/刷新</td>
        <td>Token + 账户信息</td>
        <td>不可</td>
    </tr>
    <tr>
        <td>策略限制</td>
        <td>`api.anthropic.com/api/claude_code/policy_limits`</td>
        <td>每小时</td>
        <td>组织策略查询</td>
        <td>可</td>
    </tr>
    <tr>
        <td>远程设置</td>
        <td>`api.anthropic.com/api/claude_code/settings`</td>
        <td>后台轮询</td>
        <td>settings.json 推送</td>
        <td>可</td>
    </tr>
    <tr>
        <td>域名检查</td>
        <td>`api.anthropic.com/api/web/domain_info`</td>
        <td>WebFetch 前</td>
        <td>目标域名</td>
        <td>不可</td>
    </tr>
</table>
<!-- END_BLOCK_140 -->

<!-- BLOCK_141 | doxlglVsze67RpOOgZ6WYs8PhAd -->
<table header-row="true" col-widths="96,392,102,190,80">
    <tr>
        <td>目标</td>
        <td>URL</td>
        <td>频率</td>
        <td>数据内容</td>
        <td>可禁用</td>
    </tr>
    <tr>
        <td>MCP 代理</td>
        <td>`mcp-proxy.anthropic.com`</td>
        <td>MCP 调用</td>
        <td>MCP 请求数据</td>
        <td>不可</td>
    </tr>
    <tr>
        <td>版本更新</td>
        <td>`storage.googleapis.com`</td>
        <td>版本检查</td>
        <td>下载更新包</td>
        <td>可</td>
    </tr>
    <tr>
        <td>Changelog</td>
        <td>`raw.githubusercontent.com`</td>
        <td>更新后</td>
        <td>获取更新日志</td>
        <td>可</td>
    </tr>
</table>
<!-- END_BLOCK_141 -->

<!-- BLOCK_142 | doxlgqWYHTep2hrXly5M9TsSpCb -->
---
<!-- END_BLOCK_142 -->

<!-- BLOCK_143 | doxlgdNanHUtXCTXnX36g1xjQvb -->
## 九、自我保护建议<!-- 标题序号: 1.9 --><!-- END_BLOCK_143 -->

<!-- BLOCK_144 | doxlgDUVp91JiIEF4HT4E7FjTqO -->
### 9.1 环境变量防护<!-- END_BLOCK_144 -->

<!-- BLOCK_145 | doxlgoVtjUGbUFaY5njy3832ruf -->
<table header-row="true" col-widths="424,354">
    <tr>
        <td>环境变量</td>
        <td>作用</td>
    </tr>
    <tr>
        <td>`DISABLE_TELEMETRY=1`</td>
        <td>禁用 Datadog + 1P 事件 + 反馈调查</td>
    </tr>
    <tr>
        <td>`CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC=1`</td>
        <td>禁用所有非必要网络（遥测 + 更新 + GrowthBook）</td>
    </tr>
    <tr>
        <td>`CLAUDE_CODE_USE_BEDROCK=1`</td>
        <td>使用 AWS Bedrock（自动禁用所有分析）</td>
    </tr>
    <tr>
        <td>`CLAUDE_CODE_USE_VERTEX=1`</td>
        <td>使用 GCP Vertex（自动禁用所有分析）</td>
    </tr>
</table>
<!-- END_BLOCK_145 -->

<!-- BLOCK_146 | doxlg5KZxGbTusHrjDkoszHtrTg -->
### 9.2 使用注意事项<!-- END_BLOCK_146 -->

<!-- BLOCK_147 | doxlguW7OUg6tF8A9RphTBnb0ug -->
<table header-row="true" col-widths="261,163,275">
    <tr>
        <td>行为</td>
        <td>风险</td>
        <td>建议</td>
    </tr>
    <tr>
        <td>多设备共享账号</td>
        <td>极高</td>
        <td>每台设备单独订阅</td>
    </tr>
    <tr>
        <td>短时间大量 API 调用</td>
        <td>高</td>
        <td>控制调用频率</td>
    </tr>
    <tr>
        <td>CI/CD 中大规模使用</td>
        <td>中</td>
        <td>使用 API Key 而非 OAuth</td>
    </tr>
    <tr>
        <td>篡改客户端/伪造 Header</td>
        <td>高</td>
        <td>不要修改官方客户端</td>
    </tr>
    <tr>
        <td>长期不升级版本</td>
        <td>低-中</td>
        <td>跟随官方更新</td>
    </tr>
</table>
<!-- END_BLOCK_147 -->

<!-- BLOCK_148 | doxlgJ17pofVKPCtGK9lMmhLUff -->
### 9.3 关键文件清理<!-- END_BLOCK_148 -->

<!-- BLOCK_149 | doxlg8L3L8hjnroRcnEzm66boxc -->
<table header-row="true" col-widths="234,230,236">
    <tr>
        <td>文件/目录</td>
        <td>内容</td>
        <td>建议</td>
    </tr>
    <tr>
        <td>`~/.claude/config.json`</td>
        <td>Device ID + 账户信息</td>
        <td>删除 `userID` 字段可重置设备标识</td>
    </tr>
    <tr>
        <td>`~/.claude/telemetry/`</td>
        <td>失败的遥测事件缓存</td>
        <td>定期清理</td>
    </tr>
    <tr>
        <td>`~/.claude/.credentials.json`</td>
        <td>明文凭证（Keychain 不可用时）</td>
        <td>确保 Keychain 可用</td>
    </tr>
    <tr>
        <td>`~/.claude/projects/`</td>
        <td>完整对话历史</td>
        <td>定期清理敏感会话</td>
    </tr>
</table>
<!-- END_BLOCK_149 -->

<!-- BLOCK_150 | doxlgDA4FNZfjzgVBXgBKKwkUWd -->
---
<!-- END_BLOCK_150 -->

<!-- BLOCK_151 | doxlg5mFHtpULvw8jPvCLp9hhTb -->
## 十、总结<!-- 标题序号: 1.10 --><!-- END_BLOCK_151 -->

<!-- BLOCK_152 | doxlg7KnEjjgEJkxaTxONfVxuTb -->
Claude Code 的数据采集体系可以总结为**三层模型**：
<!-- END_BLOCK_152 -->

<!-- BLOCK_153 | doxlgbskHLsMi8J4dYvtOCIBOwg -->
<table header-row="true" col-widths="125,400,145">
    <tr>
        <td>层级</td>
        <td>内容</td>
        <td>目的</td>
    </tr>
    <tr>
        <td>**身份层**</td>
        <td>Device ID + Account UUID + Email + Fingerprint</td>
        <td>精确追踪用户</td>
    </tr>
    <tr>
        <td>**环境层**</td>
        <td>OS + 架构 + 终端 + CI + 容器 + 部署环境</td>
        <td>构建设备指纹</td>
    </tr>
    <tr>
        <td>**行为层**</td>
        <td>640+ 事件 + Token 用量 + 工具调用 + 进程资源</td>
        <td>分析使用模式</td>
    </tr>
</table>
<!-- END_BLOCK_153 -->

<!-- BLOCK_154 | doxlg1p10esvWvV1s1rr47bn3Ed -->
这三层数据通过三条通道（1P API + Datadog + GrowthBook）实时上报，服务端拥有对每个用户**完整的使用画像**，可以从多个维度检测异常并触发封号决策。
<!-- END_BLOCK_154 -->

<!-- BLOCK_155 | doxlgNb5jVQMeQ0MUH2L7w722Tg -->
<callout icon="gift" bgc="4" bc="1">
最安全的做法：使用 Bedrock/Vertex 第三方提供商（自动禁用所有分析），或设置`DISABLE_TELEMETRY=1` + 控制使用频率 + 一人一号。
</callout>
<!-- END_BLOCK_155 -->

