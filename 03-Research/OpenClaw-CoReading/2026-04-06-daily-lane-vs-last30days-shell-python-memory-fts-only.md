# 2026-04-06｜daily-lane vs last30days：shell / Python 取舍，以及 OpenClaw memory 的 FTS-only 现状

## 背景

今天围绕两个相关问题做了一轮实地排查：

1. `daily-lane` 这种 lane-first、signal-first 的系统，后续应该继续 shell，还是迁到 Python / TS。
2. OpenClaw builtin memory 到底是不是只能走向量化；如果不是，为什么当前体验上仍然像“被 embeddings 绑定”。

这轮不是抽象判断，而是直接对本机项目、配置、源码和运行结果做的核实。

---

## 一、先说结论

### 1. `daily-lane` 当前确实是 shell-first runtime

主入口：
- `daily-lane/run.sh`

lane 实现：
- `lanes/github-watch.sh`
- `lanes/product-hunt-watch.sh`
- `lanes/github-trending-weekly.sh`
- `lanes/x-feed.sh`
- `lanes/x-following.sh`

辅助层：
- `lib/*.sh`

特点：
- 用 shell 做总调度和 lane 编排
- 配置解析时借 Python 处理 YAML，再回到 shell + `jq`
- 依赖明显是 CLI-first：`gh`、`jq`、`python3`、`git`、`curl` 等

所以它不是“纯 shell”，但**系统骨架和运行主链明确是 shell-first**。

### 2. `last30days` 现在确实是 Python 项目

本机项目：
- `/Users/haha/.openclaw/workspace/last30days-skill`

直接证据：
- 主入口是 `scripts/last30days.py`
- shebang 是 `#!/usr/bin/env python3`
- `README.md` 的 usage 也是 `python3 last30days.py <topic> [options]`
- `tests/` 下是一整套 Python 测试，如 `test_query.py`、`test_relevance.py`、`test_cache.py`、`test_status_banner.py` 等
- `scripts/` 下核心脚本也是 Python：`last30days.py`、`store.py`、`watchlist.py`、`briefing.py` 等

所以它不是“边角有 Python”，而是**主实现就是 Python**。

### 3. 两者为什么语言不同：不是风格矛盾，而是系统类型不同

#### `daily-lane` 更像：
- lane-first 收集 runtime
- CLI/API 调度脚本
- 明确的文件产出链
- 稳定执行、低智能、低魔法

#### `last30days` 更像：
- research engine / retrieval program
- 多源搜索聚合
- normalize / score / dedupe / synthesize
- 有 mock / diagnose / fixtures / tests / richer internal state

也就是说：

> `last30days` 更像“小型程序”；`daily-lane` 当前更像“稳定脚本系统”。

所以不能简单用“last30days 是 Python，所以 lane 也该马上迁 Python”来推导。

---

## 二、关于 `daily-lane` 后续语言方向的判断

## 当前判断

### 短期：继续 shell

原因：
- 当前 runtime 已经成型，`run.sh + lanes/*.sh + lib/*.sh` 是真实产线
- lane 第一层的核心工作仍然是：调 CLI / API、写文件、写状态、做 git 同步
- 这层工作 shell 依然合适
- 现在最紧要的问题是系统边界清晰化，而不是整仓迁移语言

### 中期：把“共性复杂能力”抽到 Python

最适合迁出的，不是 lane 主流程，而是：
- diagnose / doctor
- check-config
- mock / fixtures
- status schema / report renderer
- 更严格的配置归一化和校验

原因：
- 这些模块对数据结构、错误处理、测试性的要求高于 shell 的舒适区
- 一旦越过“简单脚本编排”，shell 很快会出现 `jq/heredoc/grep/awk` 混战、错误码脆弱、测试难维护的问题

### 暂不建议：迁到 TS

理由：
- 当前项目不是 Node/TS-first runtime
- 依赖和运维现实都更偏 shell + CLI + 少量 Python
- TS 的收益（类型系统、Node SDK、前后端共享模型）在当前 lane 系统里吃不满
- 反而会引入额外的 runtime / build / 包管理复杂度

## 最终判断

> 主流程继续 shell；共性复杂能力逐步转 Python；暂时不值得上 TS。

这比“全部留 shell”或“现在全迁 Python”都更稳。

---

## 三、OpenClaw memory 到底是不是只能向量化

答案：**不是。**

### 能力层面

OpenClaw builtin memory 不是只有 vector search，而是至少有三层：
- keyword search（FTS5）
- vector search（embeddings）
- hybrid search（keyword + vector）

官方文档 `docs/concepts/memory-builtin.md` 直接写过：

> Without an embedding provider, only keyword search is available.

### 本机代码层面

从 OpenClaw 本地安装代码里能看到明确逻辑：

```js
const searchMode = this.provider ? "hybrid" : "fts-only";
```

也就是说：
- 有 provider → hybrid
- 无 provider → fts-only

并且搜索逻辑里，当 `!this.provider` 时，确实会走 FTS/keyword 路径。

### 但产品配置层面

问题在于：**当前 schema 没把 FTS-only 做成好用的一等配置。**

本机验证结果：
- `memorySearch.provider` 允许值只有：
  - `openai`
  - `local`
  - `gemini`
  - `voyage`
  - `mistral`
  - `ollama`
- 直接写 `provider: "none"` 会被 schema 拒绝

所以现状不是“没有 FTS-only”，而是：

> 底层有 FTS-only 语义，但配置层没有把它明确暴露成一个稳定、正式、易用的模式。

---

## 四、这次 memory 搜索故障的实际结论

### 原始问题

`memory_search` 失败，报：
- `gemini embeddings failed: 429`
- `RESOURCE_EXHAUSTED`

### 核心结论

不是 memory 文件坏了，而是：
- 当前 memory 检索链依赖 Gemini embeddings
- embedding provider 配额耗尽
- 导致语义检索不可用

### 临时处理

已把本机 OpenClaw 配置改成：
- `agents.defaults.memorySearch.provider = "local"`
- `fallback = "none"`

结果：
- 本地 embedding 模型成功下载
- `openclaw memory status` 显示 provider 已切到 `local`
- `openclaw memory index --force` 能成功运行
- CLI 侧 memory search 不再因为 Gemini 429 直接崩掉

### 但它不是最终答案

因为切到 local 之后，虽然“能用”恢复了，但检索命中/排序仍不够稳；这说明：
- local 解决了可用性
- 没解决“像 grep 一样可预期”的检索体验问题

因此长期更合理的方向仍然是：

> 把 OpenClaw memory 的 FTS-only / keyword-only 模式正式产品化，而不是永远把 memory 绑定在 embedding provider 上。

---

## 五、与社区/上游现状的对应

这不是本机独有问题。

GitHub 上已存在与 FTS-only 相关的上游 issue，例如：
- `openclaw/openclaw#39224`：FTS-only / `provider=none` 下 index/search 异常
- `openclaw/openclaw#43957`：支持 FTS-only memory indexing 的提案
- `openclaw/openclaw#26792`：历史上 FTS-only broken 的 bug

这进一步说明：

> OpenClaw 社区已经承认“非向量 memory”是合理需求，只是当前产品化和稳定性还没完全收好。

---

## 六、对 lane 后续方向的启发

这轮对比给 `daily-lane` 的启发，不是“马上抄 last30days 上 Python”，而是更细一点：

### 1. 先守住 lane 的本质
- 稳定脚本
- 收集优先
- 低魔法
- 低智能
- 文件产物清楚

### 2. 再识别哪些模块已经不像脚本，而像程序
优先迁 Python 的，应该是这些“运行纪律层”能力：
- diagnose
- check-config
- mock / fixtures
- status / summary / schema

### 3. 语言迁移的触发条件，不应靠主观偏好，而应靠复杂度信号
当出现下面这些信号时，说明 shell 可能该让位：
- 数据结构越来越复杂
- diagnose / status / mock 占比快速升高
- 错误分类越来越细
- 测试痛苦开始超过改代码本身
- shell 中出现大量 JSON 拼接、`jq`/`awk`/`grep` 交错逻辑

---

## 七、一句话总结

> `last30days` 是 Python，因为它本质上是 research engine。`daily-lane` 现在仍是 shell-first，因为它本质上还是 lane runtime。真正合理的方向不是“马上全迁”，而是：主流程继续 shell，共性复杂能力逐步转 Python，同时把 OpenClaw 这类系统里已经存在但没产品化好的 FTS-only/keyword-only 模式当成正式能力去补齐。
