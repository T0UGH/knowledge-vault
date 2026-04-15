# Hermes：如何建立一个新的 profile

- 日期：2026-04-15
- 结论来源：直接阅读本机 Hermes 代码：
  - `~/.hermes/hermes-agent/hermes_cli/profiles.py`
  - `~/.hermes/hermes-agent/hermes_cli/main.py`

## 先说结论
Hermes 的 profile 是**完整隔离的 HERMES_HOME**。

每个 profile 都有自己独立的：
- `config.yaml`
- `.env`
- `memory`
- `sessions`
- `skills`
- `cron`
- `logs`
- 以及一个独立的 `home/` 目录（给 subprocess 隔离 git/ssh/gh/npm 等工具配置）

默认 profile 还是：
- `~/.hermes`

新 profile 默认放在：
- `~/.hermes/profiles/<name>/`

## 最常用命令

### 1. 新建一个全新 profile
```bash
hermes profile create coder
```
作用：
- 创建新目录：`~/.hermes/profiles/coder/`
- 初始化基础目录结构
- 自动同步 bundled skills
- 尝试创建一个命令别名 wrapper：
  - `~/.local/bin/coder`

创建后可以这样用：
```bash
coder chat
```
或者不用 alias，直接：
```bash
hermes -p coder chat
```

---

### 2. 基于当前 profile 克隆配置创建
```bash
hermes profile create coder --clone
```
作用：
- 新建 profile
- 复制当前 active profile 的关键配置文件：
  - `config.yaml`
  - `.env`
  - `SOUL.md`
  - `memories/MEMORY.md`
  - `memories/USER.md`
- 不复制全部运行时垃圾

适合场景：
- 想快速做一个“差不多但独立”的 agent
- 继承模型/API key/人格/记忆基础，但不继承会话历史和运行状态

---

### 3. 完整拷贝一个 profile
```bash
hermes profile create coder --clone-all
```
作用：
- 直接 full copy 源 profile 目录
- 但会清掉少数运行时文件，比如：
  - `gateway.pid`
  - `gateway_state.json`
  - `processes.json`

适合场景：
- 想最大程度复刻一个现有 profile

不太适合场景：
- 你只是想建一个干净的新 agent

---

### 4. 指定从哪个 profile 克隆
```bash
hermes profile create coder --clone --clone-from research
```
或：
```bash
hermes profile create coder --clone-all --clone-from research
```

如果不写 `--clone-from`，代码里默认会从**当前 active profile**克隆。

---

### 5. 切换默认 profile（sticky default）
```bash
hermes profile use coder
```
作用：
- 在 `~/.hermes/active_profile` 里写入 `coder`
- 之后直接运行 `hermes` 时，会默认进入这个 profile

切回默认内建 profile：
```bash
hermes profile use default
```

## 查看 / 管理 profile

### 查看当前 active profile
```bash
hermes profile
```

### 列出所有 profile
```bash
hermes profile list
```

### 查看某个 profile 详情
```bash
hermes profile show coder
```

### 删除 profile
```bash
hermes profile delete coder
```

会删除：
- profile 目录
- alias wrapper
- 相关 gateway/service 状态

## alias / wrapper 机制
Hermes 会尝试给新 profile 创建 wrapper：
- 位置：`~/.local/bin/<profile-name>`
- 内容本质上就是：
```sh
exec hermes -p <profile-name> "$@"
```

例如 `coder` wrapper 等价于：
```bash
hermes -p coder ...
```

### 如果 alias 没生效，先检查 PATH
代码里明确检查的是：
- `~/.local/bin` 是否在 `PATH`

如果没有，补到 shell 配置：
```bash
export PATH="$HOME/.local/bin:$PATH"
```

## profile 名称限制
代码里的规则大致是：
- 必须匹配：
  - `^[a-z0-9][a-z0-9_-]{0,63}$`
- 也就是：
  - 小写字母
  - 数字
  - `_`
  - `-`
- 不能乱用空格、大写、特殊字符

另外这些名字会被拒绝：
- 保留名：`default`, `hermes`, `root`, `sudo`, `tmp`, `test` 等
- Hermes 子命令名：
  - `chat`, `setup`, `gateway`, `cron`, `doctor`, `profile`, `honcho` 等

所以安全命名建议：
- `coder`
- `research`
- `ops`
- `work`
- `personal`

## 实际推荐用法

### 场景 A：建一个干净的新 agent
推荐：
```bash
hermes profile create coder
coder setup
```

### 场景 B：基于当前人格/配置快速分身
推荐：
```bash
hermes profile create research --clone
research chat
```

### 场景 C：让某个 profile 成为日常默认
```bash
hermes profile use research
hermes
```

## 代码层面值得知道的细节

### 1. `-p/--profile` 是启动前预解析的
`main.py` 会在大部分模块 import 之前，就先处理：
- `hermes -p coder ...`

然后把：
- `HERMES_HOME`
设置成对应 profile 目录。

这很重要，因为很多模块会在 import 时缓存路径。

### 2. sticky default 来自 `~/.hermes/active_profile`
如果你没传 `-p`，Hermes 会读：
- `~/.hermes/active_profile`

如果里面写的是非 `default`，就自动切到那个 profile。

### 3. profile 不只是聊天配置隔离
代码里还给每个 profile 建了：
- `home/`

这个是给 subprocess 用的 profile-scoped HOME，目的是避免：
- git 配置
- ssh 配置
- gh 登录
- npm 配置
- 其他系统工具状态
在不同 profile 之间串掉。

这说明 Hermes 的 profile 设计目标不是“表面多身份”，而是**尽量完整的运行时隔离**。

## 最简操作清单
如果只是想新建一个 profile，最短路径是：

```bash
hermes profile create coder --clone
hermes profile use coder
coder setup
coder chat
```

如果你要绝对干净：

```bash
hermes profile create coder
coder setup
coder chat
```

## 备注
这篇笔记回答的是：
- Hermes 怎么建立新 profile
- profile 实际隔离了什么
- 常用命令和推荐做法

如果后面要继续研究：
- 不同 profile 的 gateway/service 怎么并存
- profile 与 Honcho / memory 插件的关系
- profile 迁移与导出

可以再单独补一篇。