# MemPalace：记忆写入机制是主动还是被动？

## 结论

MemPalace **不是纯被动写入系统**。

更准确地说：

- **基础设计偏主动写入**
- **集成层补了一层被动触发/自动提醒**
- **真正的写入执行本身，仍然主要依赖 agent 或用户主动完成**

一句话概括：

> **写入动作本质上是主动的，触发时机后来又加了被动自动化。**

---

## 为什么说它“基础设计偏主动”

### 1. `mine` 是第一公民

MemPalace 最原始、最明确的写入方式是：

```bash
mempalace mine <dir>
mempalace mine <dir> --mode convos
```

这意味着：

- 你主动指定目录
- 系统主动 ingest
- 建立 palace / index / searchable memory

这是一种非常明确的**主动写入模型**，而不是后台无感持续监听。

### 2. hook 本身不负责理解内容，只负责“叫你去存”

项目文档里有一句很关键：

> The AI does the actual filing ... The hooks just tell it WHEN to save.

这句话的含义是：

- hook 不负责判断内容应该怎么归档
- hook 不负责理解“什么值得记”
- hook 只是到了某个 checkpoint，要求 AI 去执行保存
- 真正把内容写进 palace、归到哪个 wing/room/closet，仍然是 AI 主动完成

所以即使有 hook，它的核心仍然不是“系统后台默默自动写库”，而是“系统在关键时点推动 agent 主动归档”。

### 3. README 的默认工作流就是 `init -> mine -> search`

从 CLI 和 README 的叙事看，它的底层哲学是：

- 先初始化 palace
- 再主动 mine 数据
- 再主动 search 和记忆召回

这说明它首先是一个**本地 memory engine / ingest system**，而不是一个以被动实时监听为核心的系统。

---

## 为什么又说它“集成层补了一层被动触发”

虽然核心写入偏主动，但在 Claude Code / Codex CLI / Gemini CLI 集成里，它加了明显的**自动触发机制**。

### 1. Stop hook：每隔 N 条消息自动触发保存

典型设计是：

- 每 15 条 human message
- 在 Stop hook 处拦一下
- 提醒 / 强制 AI 现在进行一次保存

所以从使用体验上，用户不必自己每次手动说“请保存”，这部分属于**被动触发**。

### 2. PreCompact hook：在上下文压缩前强制保存

在上下文即将 compact 前：

- hook 总是触发
- 强制保存一次
- 避免上下文丢失

这也是明显的**系统自动触发**。

### 3. `MEMPAL_DIR` 模式下可以自动跑 `mempalace mine`

如果设置了 `MEMPAL_DIR`，hook 还能在触发时自动执行：

```bash
mempalace mine "$MEMPAL_DIR"
```

这一层更接近**被动自动 ingest**。

但即便如此，它依然不是“什么都不用设计，系统自己全懂全写”的模式；它只是把“何时写入”这件事自动化了。

---

## 最准确的理解方式

可以把 MemPalace 的写入机制拆成两层看：

### A. 写入触发（When to save）

这层后来变得越来越自动：

- Stop hook
- PreCompact hook
- 可选 auto-ingest

所以这一层带有明显的**被动 / 自动触发特征**。

### B. 写入执行（How to file）

这层仍然更偏主动：

- 用户主动 `mine`
- 或 agent 被明确要求去保存
- AI 负责理解上下文并归档到合适位置

所以这一层本质仍然是**主动写入**。

---

## 最终判断

如果只问一句：

**MemPalace 在原本设计里，记忆写入是主动的还是被动的？**

更准确的答案是：

> **核心是主动写入；为了不丢上下文，后面又叠加了被动触发的自动保存机制。**

再压缩一点：

> **默认写入模型：主动。默认触发增强：半自动。**

---

## 对比一句话

它不是那种：

> 系统在后台持续监听、静默理解、自动写库

它更像：

> 记忆库一直在那里；平时通过 `mine` 主动灌入，或在关键节点由 hook 自动提醒 / 强制 agent 主动归档。
