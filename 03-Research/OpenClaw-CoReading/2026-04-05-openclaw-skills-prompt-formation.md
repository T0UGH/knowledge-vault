---
title: OpenClaw 共读：skills prompt 形成机制
date: 2026-04-05
tags:
  - openclaw
  - co-reading
  - architecture
  - skills
  - prompt
status: active
---

# OpenClaw 共读：skills prompt 形成机制

## 一句话结论

OpenClaw 的 skills prompt 不是每轮临时扫目录现拼出来的，而是一条 **“发现技能 → 过滤可见性/适用性 → 生成 snapshot → 会话持久化 → run 时复用 prompt”** 的链路。换句话说，它真正管理的不是一段 `<available_skills>` 文本，而是 **技能目录的稳定快照**。

## 这部分在系统里负责什么

这一层主要解决 4 个问题：

- workspace / managed / bundled / plugin / remote 技能，最终哪些能进入当前 agent 的视野
- 这些技能如何被格式化成 `<available_skills>` 提示块
- 为什么不是每轮都重新扫描技能目录
- 技能目录变化后，什么时候应该刷新当前 session 看到的 skills prompt

如果没有这层，前面那篇 prompt composition 里的 `skillsPrompt` 就只是黑盒变量。

而这篇真正回答的是：

> **`skillsPrompt` 从哪来、什么时候变、为什么能在 session 层保持相对稳定。**

## 主链路

我这轮抓的主链路是这几段：

1. **workspace 技能被解析成可见 skill entries**  
   `src/agents/skills/workspace.ts`
2. **skill entries 经过 filter / eligibility / visibility 决定哪些能进 prompt**  
   `src/agents/skills/filter.ts`
3. **最终生成 `SkillSnapshot`，里面同时保存 prompt、skills、resolvedSkills、version**  
   `src/agents/skills/workspace.ts`
4. **reply session 首轮或技能变化时，把 snapshot 持久化到 session entry**  
   `src/auto-reply/reply/session-updates.ts`
5. **embedded run 运行前优先从 `skillsSnapshot` 直接取 `skillsPrompt`**  
   `src/agents/pi-embedded-runner/run/attempt.ts`
6. **skills watcher 只盯 `SKILL.md` 变化，并 bump snapshot version**  
   `src/agents/skills/refresh.ts`
7. **最终 `skillsPrompt` 再进入 system prompt builder 的 Skills section**  
   `src/agents/system-prompt.ts`

一句话串起来：

> **OpenClaw 先把技能目录收敛成一个受控 snapshot，session 再持有这个 snapshot，run 时优先复用 snapshot prompt，而不是每轮都重新扫技能树。**

## 关键文件与代码片段

### 1. `workspace.ts`：skills prompt 的真正源头在 workspace skill snapshot

这轮最关键的函数就是：

```ts
export function buildWorkspaceSkillSnapshot(
  workspaceDir: string,
  opts?: WorkspaceSkillBuildOptions & { snapshotVersion?: number },
): SkillSnapshot {
  const { eligible, prompt, resolvedSkills } = resolveWorkspaceSkillPromptState(workspaceDir, opts);
  ...
  return {
    prompt,
    skills: eligible.map(...),
    ...(skillFilter === undefined ? {} : { skillFilter }),
    resolvedSkills,
    version: opts?.snapshotVersion,
  };
}
```

这里已经把设计意图说得很明白：

- `prompt`：直接给 system prompt 用的 skills 提示块
- `skills`：轻量摘要
- `resolvedSkills`：更完整的技能对象
- `skillFilter`：当前 agent/session 适用的 filter
- `version`：用来判断是否应该刷新

我的判断：

> **OpenClaw 真正缓存的是“技能视图快照”，不是只缓存一段字符串。**

### 2. `workspace.ts`：`<available_skills>` 只是最后一层呈现格式

真正格式化时会拼这种结构：

```ts
<available_skills>
  <skill>
    <name>...</name>
    <location>...</location>
  </skill>
</available_skills>
```

并且文件里还会额外加前导约束，比如：

- 技能说明触发方式
- 只能先读一个 skill
- 相对路径要相对 `SKILL.md` 目录解析

也就是说，skills prompt 不是技能列表而已，而是：

> **技能目录 + 使用协议**

### 3. `workspace.ts`：进入 prompt 之前，技能会先过预算裁剪

这里特别值得看：

```ts
const { skillsForPrompt, truncated, compact } = applySkillsPromptLimits({
  skills: promptSkills,
  config: opts?.config,
})
```

如果完整格式放不下，就会：

- 退到 compact format
- 还不够再做二分裁剪
- 最后加 truncation note

这说明 OpenClaw 对 skills prompt 的看法很成熟：

> **技能目录不是越全越好，而是要在提示预算内保留尽可能有效的技能可见性。**

这和前一篇 prompt composition 里看到的 bootstrap/context 预算观是一致的。

### 4. `filter.ts`：skill filter 不只是“可见 / 不可见”，还是 session 复用的判据

`matchesSkillFilter()` 这段很短，但很关键：

```ts
export function matchesSkillFilter(cached?, next?) {
  const cachedNormalized = normalizeSkillFilterForComparison(cached);
  const nextNormalized = normalizeSkillFilterForComparison(next);
  ...
}
```

这说明 skill filter 不是临时参数，而是会影响：

- 当前 snapshot 能不能继续复用
- 会不会触发 session skills snapshot refresh

也就是说，OpenClaw 不是只缓存“有一份 snapshot”，而是缓存：

> **在某个 skillFilter 约束下的 snapshot。**

### 5. `session-updates.ts`：skills snapshot 被写进 session entry，而不是 run 临时变量

这段是这轮最重要的运行期逻辑之一：

```ts
const snapshotVersion = getSkillsSnapshotVersion(workspaceDir);
const existingSnapshot = nextEntry?.skillsSnapshot;
ensureSkillsWatcher({ workspaceDir, config: cfg });
const shouldRefreshSnapshot =
  shouldRefreshSnapshotForVersion(existingSnapshot?.version, snapshotVersion) ||
  !matchesSkillFilter(existingSnapshot?.skillFilter, skillFilter);
```

然后会：

- 首轮 session 直接写入 snapshot
- 非首轮如果版本变化或 filter 不匹配，也更新 snapshot

这说明 skills prompt 的稳定性不是“永远不变”，而是：

> **对单个 session 来说尽量稳定，但当技能目录或适用范围变化时，会有明确刷新机制。**

这个设计我很认同。因为如果每轮都重扫技能目录，运行成本和上下文抖动都会更大；如果永远不刷新，又会让技能视图过期。

### 6. `refresh.ts`：watcher 只盯 `SKILL.md`，说明它盯的是技能定义，而不是整个目录树

这块也很工程化。

```ts
targets.add(`${globRoot}/SKILL.md`);
targets.add(`${globRoot}/*/SKILL.md`);
```

并且注释写得很清楚：

- 只 watch `SKILL.md`
- 避免遍历大树
- 减少 macOS FD exhaustion

这说明 OpenClaw 把“什么算技能变化”定义得很明确：

> **技能变化以 `SKILL.md` 为主，不以整个技能目录任何文件变化为主。**

这有取舍，但取舍很实际。

### 7. `run/attempt.ts`：run 时优先用 snapshot，而不是即时重建

这段把整个链路闭环了：

```ts
const skillsPrompt = resolveSkillsPromptForRun({
  skillsSnapshot: params.skillsSnapshot,
  entries: shouldLoadSkillEntries ? skillEntries : undefined,
  config: params.config,
  workspaceDir: effectiveWorkspace,
  agentId: sessionAgentId,
});
```

而 `resolveSkillsPromptForRun()` 的第一优先级就是：

```ts
const snapshotPrompt = params.skillsSnapshot?.prompt?.trim();
if (snapshotPrompt) {
  return snapshotPrompt;
}
```

这说明：

- 正常路径：直接吃 session 里已有的 snapshot prompt
- fallback 路径：只有必要时才根据 entries 现算

这个判断很对，因为 skills prompt 对单个 session 本来就应该相对稳定。

### 8. `system-prompt.ts`：skills 在最终 prompt 里是独立 section，不是散落说明

前一篇看过 `buildSkillsSection()`，这次回头看它更有感觉。

它会把：

- 技能使用规则
- 只能先读一个 skill 的纪律
- rate limit 提示
- `<available_skills>` block

一起塞进 `## Skills (mandatory)` 这一节。

一句话判断：

> **skills prompt 在 OpenClaw 里不是“可选提示”，而是 system prompt 的一级结构。**

这就是为什么技能系统能成为平台能力，而不是外挂说明书。

## 设计判断

### 1. OpenClaw 的技能系统核心不是 loader，而是 snapshot

这轮我最核心的判断就是这个。

大家表面上容易盯着：

- 技能从哪扫出来
- 怎么找 `SKILL.md`
- 怎么过滤名字

但真正让系统稳住的，是 `SkillSnapshot`：

- 它把可见技能集合冻结成一个版本化快照
- 它让 session 能持有稳定技能视图
- 它让 run 不用每轮都重新扫技能树

所以 skills prompt 真正的核心对象不是 prompt string，而是 snapshot。

### 2. Skills prompt 是“技能视图”，不是“技能真相”

这一点也很重要。

- `resolvedSkills` 更接近技能真相
- `prompt` 只是给模型看的那层视图
- `compact/truncated` 进一步说明 prompt 视图可以退化，但 runtime 真相仍然完整

也就是说：

> **模型看到的 skills prompt 是受预算和可见性约束后的展示层。**

不是技能系统的全部真相。

### 3. 版本刷新 + filter 匹配，让 skills prompt 既稳定又可演进

这套机制很成熟：

- 不每轮重建，避免抖动
- 技能变更时自动 bump 版本
- filter 变化时也能触发刷新

这正好解决了技能系统的两个常见坑：

- 太动态，prompt 每轮乱跳
- 太静态，技能改了 session 也不知道

### 4. `SKILL.md` 是平台契约，不只是说明文档

看完整条链路后，这点越来越清楚：

- watcher 盯 `SKILL.md`
- loader 认 `SKILL.md`
- prompt 里给出 `SKILL.md` location
- 系统指令要求先读 `SKILL.md`

所以 `SKILL.md` 在 OpenClaw 里不是普通 README，而是：

> **技能被平台发现、描述、提示、调用的正式契约入口。**

### 5. 技能系统的真正成熟点在于“可治理”

这轮能看到不少治理味道：

- path compacting
- prompt budget
- truncated note
- filter matching
- watcher debounce
- snapshot versioning

这说明 OpenClaw 不是把技能当“能用就行”的插件目录，而是在认真治理技能系统的提示成本和刷新成本。

## 关键术语解释

- **skills prompt**：最终注入 system prompt 的技能可见性提示块，通常包含 `<available_skills>` 列表和使用规则。
- **SkillSnapshot**：技能系统对当前 session / workspace 可见技能视图的版本化快照。
- **resolvedSkills**：更完整的技能对象集合，比 prompt string 更接近技能真相层。
- **skillFilter**：限制当前 agent/session 可见技能集合的过滤条件。
- **snapshot version**：用于判断技能快照是否过期、是否需要刷新的一致性版本号。
- **compact format**：当技能提示块过大时，退化成更短格式的展示方式。
- **truncation note**：技能过多或提示过长时，对 prompt 裁剪结果的明确提示。
- **skills watcher**：监听 `SKILL.md` 变化并 bump snapshot version 的监视器。
- **SKILL.md**：OpenClaw 技能的正式契约入口文件，不只是说明文档。

## 本轮遗留问题

1. `loadSkillEntries()` 如何合并 workspace / managed / bundled / plugin / remote skill roots，这一层还可以单独深挖一篇。
2. remote skill eligibility 的真实策略和缓存机制，这轮只触到接口，还没完整展开。
3. `applySkillEnvOverridesFromSnapshot()` 说明技能系统还会影响运行时环境变量，这部分很值得专门读一篇。
4. `resolvedSkills` 与真正执行时的本地/远程技能调用边界，后面可以跟 tool runtime 一起对照。

## Pipeline Status
- Obsidian draft: done
- Term explanation: done
- Humanizer: done (light pass)
- Feishu publish: done (`https://feishu.cn/docx/Lvbsdm11roSOQRxUBOScqyaanwf`)
- Vault git sync: in progress
