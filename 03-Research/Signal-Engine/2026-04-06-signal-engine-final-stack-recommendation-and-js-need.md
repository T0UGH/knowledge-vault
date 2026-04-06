# 2026-04-06｜Signal Engine 最终技术栈判断，以及当前几条 lane 是否需要 JS

## 背景

这个问题分两层：

1. **如果 Signal Engine 最终真的长成一个完整系统，推荐什么技术栈？**
2. **如果只看当前这几条 lane，是否已经有引入 JS/TS 的必要？**

这页的核心结论是：
- 最终形态仍推荐 **Python 作为核心技术栈**
- 如果只看当前几条 lane，**暂时没有引入 JS 作为核心 runtime 的必要**

---

## 一、如果 Signal Engine 最终长成完整系统，推荐什么技术栈

### 核心推荐：Python

更具体地说：
- **CLI / runtime / lane orchestration**：Python
- **配置与数据模型**：Pydantic
- **CLI 框架**：Typer 或 Click
- **HTTP / 抓取**：httpx
- **结构化状态与中间数据**：文件系统 + JSON/JSONL + SQLite
- **测试**：pytest

### 为什么不是 TS 作为核心

因为 Signal Engine 最终更像：
- collect engine
- diagnose / status / config-check / mock runtime
- stateful lanes
- 结构化文件产物系统
- 未来可能有 deterministic processing layer

这更像“数据/状态/流程很重的后端程序”，而不是浏览器前端平台或 Node CLI hub。

Python 在这里的优势更贴脸：
- 更适合程序型 pipeline
- 更适合 diagnose / mock / replay / fixtures
- 更适合 state / file / data handling
- 本机已有 `last30days` 这个 Python 参照

### 更准确的推荐语句

> Python 做内核，TS 只在未来真有 UI / dashboard / 平台外壳时再做壳层。

---

## 二、如果只看当前这几条 lane，有 JS 的必要吗

### 当前结论：没有明显必要

如果只看目前讨论的这几条 lane：
- `x-feed`
- `x-following`
- `github-trending-weekly`
- `github-watch`
- `product-hunt-watch`

它们的共性是：
- collect-first
- CLI/API/file/state 编排为主
- 产物是 markdown / signals / index / state
- 未来近期要补的是 diagnose / status / config-check / mock

这类问题的核心不是“浏览器 UI 控制平台”，而是：
- 运行时边界
- 统一输出协议
- state 读写
- 可观测性
- 可测试性

这些都更像 Python 的主场。

### 为什么暂时不需要 JS

#### 1. 当前 lane 不是前端生态问题
不是在做：
- 浏览器插件
- React 前端
- Electron shell
- Node SDK 平台

#### 2. 当前 lane 的核心不是网页交互复杂性，而是 collect/runtime 复杂性
即使像 `x-feed` / `x-following` 会调用 `opencli`，Signal Engine 也只是把 `opencli` 当 external capability/source，不需要自己变成浏览器自动化框架。

#### 3. 引入 JS/TS 不会天然减少当前迁移复杂度
如果现在把核心 runtime 写成 TS：
- 还是要重新定义 lane runtime
- 还是要重新做 state / run.json / outputs / diagnose
- 只是把复杂度搬到了另一种语言里

所以如果只看这几条 lane，引入 JS 不会明显降低问题难度。

---

## 三、什么时候 JS/TS 才会开始变得有必要

如果未来 Signal Engine 变成下面这类系统，JS/TS 的必要性会提高：

- 有真正的 Web dashboard / UI
- 有前端共享 schema / model 的需求
- 有插件市场 / adapter marketplace
- 有重 browser automation / browser-native adapter 生态
- 有强 Node/browser/CDP 集成需求
- 产品重心从 collect engine 转向“平台化 CLI hub”

但这不是当前 lane 的画像。

---

## 四、结论

### 最终形态技术栈判断
> Signal Engine 的核心仍推荐 Python。Python 做 collect/runtime/state/diagnose 内核；TS 只在未来确实出现 UI 或平台外壳需求时再补。

### 当前几条 lane 的判断
> 如果只看现在的几条 lane，没有明显的 JS 必要性。当前问题的本质是 collect runtime、状态、输出协议和运行纪律层，这些都更适合直接用 Python 统一，而不是额外引入 JS/TS 作为核心 runtime。
