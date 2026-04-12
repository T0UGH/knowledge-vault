---
title: Codex plugin / hooks / Claude Code 兼容性调研
created: 2026-04-09
updated: 2026-04-09
tags:
  - research
  - codex
  - claude-code
  - plugins
  - hooks
---

# Codex plugin / hooks / Claude Code 兼容性调研

## 结论

- **Codex 的 hooks 是真成熟功能，可正式用。**
- **Codex 的 plugin 也是真功能，但定位更像本地 bundle / marketplace 插件，不是通用 IDE extension。**
- **Codex 不能直接兼容 Claude Code 的 plugin。**
- **能迁移的是能力，不是插件包本身。** 最容易迁的是 MCP、部分脚本、部分 prompt/skill；最难迁的是插件 manifest、生命周期、发现机制和 UI 绑定。

## 本次源码核验方式

- 直接拉取 `openai/codex` 主干源码到本地临时目录审查
- 重点查看：
  - `codex-rs/hooks/...`
  - `codex-rs/core/src/hook_runtime.rs`
  - `codex-rs/core/src/plugins/...`
  - `codex-rs/app-server/tests/suite/v2/plugin_*.rs`
  - `codex-rs/skills/src/assets/samples/plugin-creator/...`

## Hooks 支持程度

### 支持的事件

当前源码明确支持 5 类 hook 事件：

- `PreToolUse`
- `PostToolUse`
- `SessionStart`
- `UserPromptSubmit`
- `Stop`

### Hooks 能做什么

不是简单通知，而是能真实影响流程：

- `PreToolUse`：可在工具执行前阻断
- `PostToolUse`：可追加 feedback / additional context
- `SessionStart`：可在会话开始时注入上下文
- `UserPromptSubmit`：可拦截用户输入
- `Stop`：可阻止结束、要求继续

### 当前限制

虽然事件面很完整，但执行器类型还没完全放开：

- **真正支持的是 `type: "command"`**
- `type: "prompt"`：源码里会被 skip，`not supported yet`
- `type: "agent"`：源码里会被 skip，`not supported yet`
- `async: true`：当前也会被 skip，`async hooks are not supported yet`

### 结论判断

> Codex hooks 的成熟度是“事件面完整、command 型执行器可正式用”，不是全形态 hook 平台。

## Plugin 支持程度

### Plugin 的真实形态

Codex plugin 当前更像：

- 本地插件 bundle
- 有自己的 manifest：`.codex-plugin/plugin.json`
- 可通过 marketplace 暴露：`.agents/plugins/marketplace.json`
- 支持 list / read / install / uninstall

### Plugin 当前明确承载的能力

源码和渲染文案都表明，plugin 当前承载：

- **skills**
- **MCP servers**
- **apps**

Codex 在提示里对 plugin 的描述基本就是：

> A plugin is a local bundle of skills, MCP servers, and apps.

### Plugin 的定位

它不是“直接执行 plugin 本体”，而是：

- 将 plugin 暴露的 skills / MCP / apps 注入会话能力面
- 如果用户明确提到某个 plugin，Codex 会倾向优先用该 plugin 关联能力

## 一个关键发现：plugin hooks 目前看起来没有真正接通

### 为什么这么判断

在 plugin sample spec / scaffold 参考里，`plugin.json` 有：

```json
"hooks": "./hooks.json"
```

但在核心实现里：

- `RawPluginManifest` 只看到解析：
  - `skills`
  - `mcpServers`
  - `apps`
  - `interface`
- `PluginManifestPaths` 里只包含：
  - `skills`
  - `mcp_servers`
  - `apps`
- **没有 `hooks` 字段**

同时，hooks 的发现逻辑是：

- 通过 config layer 遍历目录
- 在每层目录里直接找 `hooks.json`
- **不是通过 plugin manifest 里的 hooks path 装载**

### 当前最稳妥的结论

> Codex 支持 hooks；Codex 支持 plugin；但 plugin 内嵌 hooks 这条链路，目前从主干实现看还没有真正闭环。

也就是说，**文档/样例先行，核心实现未完全跟上**。

## 能否兼容 Claude Code plugin

## 结论

- **不能直接兼容。**
- 没看到 Codex 对 Claude Code plugin manifest、目录结构、生命周期或发现机制的直接兼容实现。

## 更精确的判断

### 可以迁移的部分

如果 Claude Code 的“plugin”本质只是这些能力：

- MCP server
- 一些 command 脚本
- prompt / skill
- hook command

那么这些**有机会人工迁移**到 Codex。

### 基本不能直接复用的部分

以下部分不应指望开箱即用：

- Claude Code 专属 manifest / 配置格式
- Claude Code 的插件发现与加载机制
- Claude Code 的 hook 语义与生命周期模型
- Claude Code 特有的 UI / command / agent 绑定逻辑

## 实战判断

如果要从 Claude Code 迁到 Codex，优先级应这样看：

1. **MCP 层最容易复用**
2. **prompt / skill 层可重写迁移**
3. **hook 层可部分映射**
4. **plugin 包格式基本不兼容**

一句话总结：

> 不是“Claude Code plugin 能不能直接在 Codex 跑”，而是“这个插件内部的能力能拆多少迁过去”。

## 评级

- **Hooks：8/10**
  - 事件面完整
  - command 型 hook 可正式用
  - prompt / agent / async 还没开放

- **Plugin：7/10**
  - 本地 bundle / marketplace 机制是实的
  - skills / MCP / apps 支持明确
  - 但还不是一个成熟的通用扩展平台

- **Plugin hooks：3~4/10**
  - 规格里写了
  - 当前主干实现里看不算真正接通

## 对后续判断的意义

如果目标是把 Codex 用进现有 AI 工程流，建议这样理解：

- **把 Codex 当成一个支持 hooks + MCP + 本地 plugin bundle 的 coding agent**
- **不要把它当成 Claude Code 插件生态的兼容运行时**
- 如果已有 Claude Code 资产，优先拆到：
  - MCP
  - prompts / skills
  - command hooks
- 不要期待“一键搬 plugin”

## 可继续做的事

- 做一份 [[Codex vs Claude Code 扩展能力对照表]]
- 选一个现有 Claude Code plugin，拆成：
  - 可直接复用
  - 需要改造
  - 不值得迁移
- 做一个 Codex 最小可运行样例：
  - `hooks.json`
  - 一个本地 plugin
  - 明确哪些今天能跑、哪些只是规格存在
