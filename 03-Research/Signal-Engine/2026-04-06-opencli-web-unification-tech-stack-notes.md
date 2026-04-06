# 2026-04-06｜OpenCLI 如何统一处理大量 Web 页面：技术栈与策略分层

## 背景

这页用来回答一个很具体的问题：

> OpenCLI 为什么能统一处理这么多 web 页面？它靠的是什么技术栈？不同页面技术栈有区别吗？

这次不是只看 README，而是结合本机 `opencli` 源码、文档和目录结构做了一次面向架构的复盘。

本机仓库：
- `/Users/haha/.openclaw/workspace/github/opencli`

---

## 一句话结论

OpenCLI 不是靠某个“万能网页爬虫”统一所有站点，而是靠：

- **统一的 CLI / registry / execution 框架**
- **统一的浏览器接入层（Browser Bridge / CDP）**
- **少数几种可访问策略（public / cookie / header / intercept / ui / desktop）**
- **YAML pipeline + TypeScript adapter 的双引擎架构**

也就是说，它统一的是“命令协议 + 执行框架”，不是假设所有页面技术栈都一样。

---

## 二、它怎么统一这么多页面

### 1. 命令面统一

OpenCLI 把不同站点的能力都收敛成：

```bash
opencli <site> <command> ...
```

这个统一不是手搓每条命令，而是通过：
- `registry`
- `commanderAdapter`
- `execution`

来做。

关键模块：
- `src/registry.ts`
- `src/commanderAdapter.ts`
- `src/execution.ts`

含义是：
- 命令先注册成统一元数据
- commander 只是 CLI 外壳
- 真正执行时再根据 command metadata 决定用什么策略、要不要浏览器、跑 pipeline 还是 func

### 2. 执行模型统一

`src/execution.ts` 是关键枢纽。

它统一处理：
- 参数校验
- 是否需要 browser session
- 选择 strategy
- 执行 TypeScript func 或 YAML pipeline
- 输出格式化

所以不同站点虽然底层差异大，但都会被压进同一个“命令执行协议”里。

### 3. 浏览器接入统一

它不是每个站点自己想办法登录，而是统一通过：

#### Browser Bridge
- Chrome Extension
- micro-daemon
- WebSocket / JSON-RPC

文档里明确写了：
- Browser Bridge extension
- daemon
- live browser connectivity

#### 或 CDP
对于 remote Chrome / Electron / app 场景，走：
- Chrome DevTools Protocol (CDP)

相关位置：
- `src/browser/*`
- `docs/guide/browser-bridge.md`
- `docs/advanced/cdp.md`

所以它统一的不是 DOM，而是统一获得一个：
- 带登录态
- 可执行脚本
- 可读页面上下文 / 网络请求

的浏览器运行环境。

### 4. 适配器按策略分层，而不是按网站框架分层

OpenCLI 最聪明的地方之一，是它不是先按 React/Vue/Next 分类，而是先按“可访问策略”分类。

文档/代码里可以看到这些策略：
- `public`
- `cookie`
- `header`
- `intercept`
- `ui`
- `desktop/CDP`

粗略可理解为：

#### `public`
直接 HTTP 请求公开接口
- 适合：HackerNews、BBC、RSS、一部分公开 API

#### `cookie`
复用 Chrome cookies 发请求
- 适合：需要登录但接口仍可直接调用的站点

#### `header`
需要补特定 header/token 的接口

#### `intercept`
通过网络请求拦截拿真实请求
- 常见于：GraphQL、XHR、复杂前端站点、X/Twitter

#### `ui`
走 DOM / accessibility / 界面交互
- 常见于：写操作、页面级交互、难以直接调接口的操作

#### `desktop / CDP`
用于 Electron / app / CEF 场景

所以对 OpenCLI 来说，页面技术栈真正重要的不是“是不是 React”，而是：

> 这个目标最稳的访问策略是什么？

---

## 三、它的技术栈是什么

### 主技术栈
- **TypeScript**
- **Node.js**
- **Commander.js**：CLI 命令组织
- **YAML**：声明式 adapter / pipeline
- **Chrome Extension + micro-daemon**：Browser Bridge
- **CDP**：Chrome DevTools Protocol
- **WebSocket / JSON-RPC**：CLI 与浏览器桥接通信

### 主要模块
- CLI / command：`src/cli.ts`、`src/commanderAdapter.ts`
- 执行 / runtime：`src/execution.ts`、`src/runtime.ts`
- 浏览器桥接：`src/browser/*`
- YAML pipeline：`src/pipeline/*`
- adapter / registry：`src/registry.ts`、`src/discovery.ts`、`src/plugin.ts`
- 输出：`src/output.ts`

---

## 四、不同页面技术栈有区别吗

有，但 OpenCLI 尽量把区别压缩在“strategy + adapter”层。

### 1. 公开数据页
特点：
- 无需登录
- 公共 API / RSS / 公开 HTML

常见实现：
- YAML pipeline
- `fetch`
- transform steps

### 2. 登录态网页
特点：
- 需要 Chrome 已登录
- 往往是 SPA / GraphQL / XHR-heavy 页面

常见实现：
- Browser Bridge
- cookie/header/intercept strategy
- YAML pipeline 或 TS adapter

### 3. Electron / Desktop 应用
特点：
- 不是普通网页
- 可能需要 CDP / AppleScript / Accessibility / CEF

文档里的例子：
- Feishu → AppleScript
- WeChat → AppleScript + Accessibility
- NeteaseMusic → CEF/CDP
- Cursor/Codex/Antigravity/ChatGPT → desktop adapters

所以它不是所有目标都用同一技术路线，而是：

> 外层统一，底层按策略和目标类型分化。

---

## 五、对 Signal Engine 的真正启发

对 Signal Engine 来说，最值得借的不是“怎么抓网页”，而是这两个思路：

### 1. 先统一 runtime 协议，再看每条 lane
未来每条 lane 都可以统一暴露：
- collect
- diagnose
- status
- config requirements
- output contract

这和 OpenCLI 的 command metadata / execution 模型类似。

### 2. 不按网站框架分类，而按访问策略分类
未来 source 层可以按：
- public fetch
- auth fetch
- browser-assisted fetch
- CLI passthrough
- stateful diff source

来抽象，而不是只按站点名硬编码。

---

## 一句话总结

> OpenCLI 能统一大量 web 页面，不是因为它找到了一种统一页面技术，而是因为它统一了 CLI 协议、执行框架、浏览器接入层和输出层，再把页面差异收敛到 strategy + adapter 层。对 Signal Engine 来说，最值得借的是这种“统一外层协议，分策略实现底层”的方法，而不是照抄它的全部平台复杂度。
