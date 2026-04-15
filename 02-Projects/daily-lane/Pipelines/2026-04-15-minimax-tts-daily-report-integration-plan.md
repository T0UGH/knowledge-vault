---
title: MiniMax 接入日报转音频方案
date: 2026-04-15
tags:
  - daily-report
  - tts
  - minimax
  - audio
status: draft
source:
  - https://platform.minimaxi.com/docs/api-reference/speech-t2a-http
---

# MiniMax 接入日报转音频方案

## 一句话结论

如果要把现有 AI 日报稳定转成一个音频文件，MiniMax 可以直接作为第一优先接入对象：它已经提供同步 HTTP TTS、WebSocket、异步长文本语音合成、音色管理/复刻，足够覆盖日报播报的 MVP 和后续升级。

---

## 1. 已确认的官方接口信息

基于 MiniMax 官方文档 `同步语音合成 HTTP` 页面，可确认：

- 接口地址：`POST https://api.minimaxi.com/v1/t2a_v2`
- 鉴权方式：`Authorization: Bearer <token>`
- 请求体关键字段：
  - `model`
  - `text`
  - `stream`
  - `voice_setting`
  - `audio_setting`
  - `pronunciation_dict`
  - `subtitle_enable`
- 文档示例模型：`speech-2.8-hd`
- 可见音频相关字段：
  - `audio_setting.sample_rate`
  - `audio_setting.bitrate`
  - `audio_setting.format`
  - `audio_setting.channel`
- 可见音色相关字段：
  - `voice_setting.voice_id`
  - `voice_setting.speed`
  - `voice_setting.vol`
  - `voice_setting.pitch`
  - `voice_setting.emotion`
- 文档可见输出格式：
  - 非流式支持：`mp3` / `wav` / `flac`
  - 流式支持：`mp3`
- 官方导航可见还提供：
  - `WSS 同步语音合成 WebSocket`
  - `异步长文本语音合成`
  - `音色快速复刻`
  - `音色设计`
  - `声音管理`

---

## 2. 为什么它适合日报场景

日报转音频最核心的不是“能不能念”，而是：

1. **长文本是否稳定**
2. **中文自然度是否够用**
3. **能否固定一个主播音色**
4. **后续能否做口播化升级**

MiniMax 这套接口之所以合适，是因为它不只是普通 TTS：

- 短文本可以直接同步 HTTP 合成
- 长文本已有异步能力，不必永远靠手动切块
- 有音色与声音管理，后续可以把日报变成固定“主播”
- 有 pronunciation / emotion 这类控制字段，适合修正 repo 名、英文缩写、产品名的读法

---

## 3. 推荐接入路线

### Phase 1：先做最小可用版（建议）

**目标：** 每天把一篇日报转成一个 mp3 文件。

#### 方案

先不要直接把 markdown 原文整篇塞给 TTS，而是走这条链路：

```text
日报 markdown
-> 朗读稿预处理
-> 分段 chunk
-> MiniMax 同步 HTTP TTS
-> 本地拼接为单个 mp3
```

#### 为什么先这样做

因为第一版最重要的是稳定和可控：

- 某一段失败可以单独重试
- 不依赖长文本异步接口的额外任务轮询
- 更容易控制每段停顿和结构
- 更容易插入片头/片尾/栏目提示音

---

## 4. MVP 的输入输出设计

### 输入

输入不是原始 markdown，而是**朗读稿文本**。

需要先做这些清洗：

1. 去掉 markdown 噪音
   - `#` / `##`
   - code block
   - tables
   - 裸 URL
2. 把项目符号改成可朗读句子
3. 把英文 repo / 产品名保留，但不要一行塞太多
4. 数字、百分比、时间改成更接近口播的表达
5. 每个 section 前补一句栏目提示

### 推荐的朗读结构

```text
开场
今日重点
Claude Code
Codex
OpenClaw
GitHub 趋势
Product Hunt
Polymarket
收尾
```

### 输出

第一版统一输出：

- `mp3`
- 单声道
- 固定采样率
- 一个最终文件

建议目录：

```text
outputs/audio-reports/YYYY-MM-DD.mp3
outputs/audio-reports/YYYY-MM-DD.json
```

其中 `json` 记录：

- source report path
- chunk 数
- 每段字符数
- 生成时长
- voice_id
- model
- 输出文件路径

---

## 5. 推荐请求参数（第一版）

以下参数以 MiniMax 文档里可见字段为准，先采用最保守配置：

```json
{
  "model": "speech-2.8-hd",
  "text": "这里是一段处理后的日报朗读文本",
  "stream": false,
  "voice_setting": {
    "voice_id": "先用官方现成中文音色",
    "speed": 1,
    "vol": 1,
    "pitch": 0,
    "emotion": "neutral"
  },
  "audio_setting": {
    "sample_rate": 32000,
    "bitrate": 128000,
    "format": "mp3",
    "channel": 1
  },
  "subtitle_enable": false
}
```

### 第一版建议

- `stream=false`
- `format=mp3`
- `emotion=neutral`
- `speed` 先只做一个小范围可调参数（例如 0.95 / 1.0 / 1.05）

不要第一版就做：

- 动态 emotion
- 多音色混用
- 句级 prosody 编排
- 复杂 pronunciation 词典

先跑通，再升级。

---

## 6. 朗读稿预处理规则（最关键）

这一层决定了最终 50% 以上的听感。

同一套 TTS，如果直接读 markdown，通常会像“文档朗读”；如果先转成适合口播的脚本，才会更像真正的日报播报。

### 6.1 总原则

#### 原则 1：不忠于原版 markdown 形式，只忠于信息
- 不需要把 `#`、bullet、链接、表格、代码块原样保留
- 需要保留的是：
  - 今天发生了什么
  - 哪些点最值得听
  - 每条的具体更新点
  - 为什么值得关注

#### 原则 2：口播优先，不做“屏幕阅读器”
- 输出目标不是“把文档念出来”
- 输出目标是“把日报讲出来”

#### 原则 3：一段只讲一个意思
- 一段里不要塞太多 repo 名、版本号、链接
- 听众只能线性接收，不能像看文档一样回扫

#### 原则 4：链接不朗读，只转成来源提示
- URL 不直接念
- `Sources:` 不直接逐条念完整链接
- 来源在第一版里只保留为：
  - “来源是 GitHub release”
  - “来源是 X 原帖”
  - “来源是 Product Hunt 页面”
  - “来源是 Polymarket 市场页”

---

### 6.2 结构重写规则

#### 输入结构
日报通常是：
- 标题
- 若干 section
- 每个 section 下若干 bullet
- 末尾 Sources

#### 输出结构
朗读稿统一重写成：

```text
片头
今天的三到五个重点
分栏目播报
收尾
```

#### 标题规则
- `# AI 日报（2026-04-15）`
- 改写为：
  - `以下是四月十五日的 AI 日报。`
  - 或 `今天是四月十五日，下面是今天的 AI 日报。`

#### 栏目标题规则
把技术标题改成口播标题：

- `## Claude Code` -> `先看 Claude Code。`
- `## Codex` -> `再看 Codex。`
- `## OpenClaw` -> `接着看 OpenClaw。`
- `## GitHub 趋势项目` -> `再看今天值得注意的 GitHub 趋势项目。`
- `## Product Hunt 新品` -> `最后补几条 Product Hunt 新品。`
- `## Polymarket 市场` -> `再看一条 Polymarket 市场信号。`

不要直接干读：
- `栏目一，Claude Code`
- `栏目二，Codex`

太硬。

---

### 6.3 内容级改写规则

#### 规则 A：bullet 改成完整口语句
原文：
- `v2.1.108 发布，新增 away summary 与 prompt cache TTL 配置。`

改写：
- `Claude Code 发布了 2.1.108。这版比较值得记的是两点：一个是 prompt cache 的 TTL 可以配得更细，另一个是加了 away summary，也就是用户离开一会儿再回来时，可以更快接上上下文。`

#### 规则 B：每条尽量回答三件事
每条内容尽量讲清：
1. 这是什么
2. 具体变了什么
3. 为什么值得听

#### 规则 C：一条信息过长时，优先拆成两到三句
不要一整句超过 45~60 个汉字的密度连续轰炸。

#### 规则 D：列表型更新不要全念完，做压缩
如果一条 release 有 7 个变化，不要 7 个都念。
保留：
- 最具体
- 最有辨识度
- 最贴近你关注面的 2~3 个

#### 规则 E：避免套话
这些句式尽量禁用：
- `原文围绕……展开，具体变化见来源`
- `值得关注`
- `进一步说明了`
- `更清楚了`
- `体现了趋势`

因为听感上几乎都是空话。

---

### 6.4 不同来源的专门规则

#### GitHub release / changelog
保留：
- 产品/仓库名
- 版本号
- 1~3 个最具体更新点

删掉：
- 大段 patch list
- 过细 PR 噪音
- 无意义的杂项修复堆叠

模板：
```text
X 发布了某个版本。这版最值得记的是……另外还有……如果你关心的是某条工作流，这次更新的意义在于……
```

#### X / 社交帖
保留：
- 谁发的
- 这条帖子的核心观点
- 为什么值得收进日报

删掉：
- 冗长引用
- 口水话
- 过多原文感叹句

模板：
```text
X 上有一条值得收的帖子，来自某某。它真正有价值的点不是情绪，而是它把什么事情讲透了。具体来说……
```

#### Product Hunt
保留：
- 产品名
- 一句话定位
- 为什么今天值得提

模板：
```text
Product Hunt 上有个值得提一下的新产品，叫某某。它的定位是……之所以今天把它放进日报，是因为……
```

#### Polymarket
保留：
- 问题本身
- 当前领先方
- 大概概率
- 为什么它和 AI 工作流/模型竞争有关

模板：
```text
Polymarket 这边，今天有一条值得看的市场是……当前领先的是……概率大概在……这条市场值得听，不是因为投机，而是因为它能反映大家对什么问题的预期正在变化。
```

---

### 6.5 英文、数字、符号处理规则

#### 英文 repo / 产品名
- 第一次出现：保留英文原名
- 后面重复出现：可只用主名

例子：
- 第一次：`anthropics/claude-code，也就是 Claude Code`
- 后面：`Claude Code`

#### 版本号
- `v2.1.108` 念成：`二点一点一零八版`
- 如果太绕，也可以保留视觉形式让 TTS 读，但脚本里最好测试听感

#### PR / issue / commit
- 第一版尽量少读
- `PR #17830` 改成：`一个编号一七八三零的合并请求`
- 如果不重要，直接删掉

#### 百分比
- `89.5%` 改成 `百分之八十九点五`

#### 时间
- `2026-04-15` 改成 `四月十五日`

#### 链接
- 不念 URL
- `[GitHub](https://...)` -> `来源是 GitHub`

#### 括号内容
- 像 `(laughs)`、`(beta)` 这类，如果不影响信息，删掉

---

### 6.6 必删内容清单

以下内容在朗读稿里默认删除：

- `Sources:` 完整链接列表
- 代码块
- markdown 表格
- 重复 section 标题
- 裸链接
- 无意义英文残句
- 只对视觉阅读有意义的括号注释
- 超长罗列型 bullet
- 纯调试/产物路径

---

### 6.7 建议的朗读稿生成流程

#### Step 1：先抽取正文骨架
保留：
- 标题
- section 标题
- 每条核心 bullet

#### Step 2：删噪音
去掉：
- markdown 语法
- links
- sources
- tables
- debug 信息

#### Step 3：做口播改写
把每个 bullet 改写成 2~3 句。

#### Step 4：补栏目过渡句
例如：
- `先看 Claude Code。`
- `再看 OpenClaw。`
- `最后补一条市场侧信号。`

#### Step 5：加开场和收尾
开场：
- `以下是今天的 AI 日报，我挑几条最值得听的内容给你过一遍。`

收尾：
- `以上就是今天这份 AI 日报。如果你愿意，下一步可以再把它压缩成更短的晨间播报版。`

---

### 6.8 输出风格建议

#### 推荐风格
- 像“信息密度高的晨报主持人”
- 中文自然
- 句子短
- 不装腔
- 重点明确

#### 不推荐风格
- 像论文摘要
- 像产品文档朗读
- 像 AI 总结腔
- 太多“值得关注、进一步说明、体现趋势”

---

### 6.9 第一版可直接实现的规则集

如果先写脚本，第一版只做这些规则就已经够用：

1. 去掉 markdown 标题符号、code block、table、裸链接
2. 删除 `Sources` 整段
3. 把 section 标题改成口播过渡句
4. 每条 bullet 改写成 2~3 句
5. 每条最多保留 2~3 个具体点
6. 数字、日期、百分比转成中文口播形式
7. 输出统一开场与结尾

只要这 7 条先做出来，音频听感通常就会比“直接朗读原文”好一个大档。

---

## 7. 工程实现建议

### 模块划分

#### A. `report_to_script.py`
负责把日报 markdown 转成“适合朗读”的脚本文本。

职责：
- 读取日报 markdown
- 抽取标题、栏目、条目
- 清洗 URL / markdown 噪音
- 输出朗读稿

#### B. `chunk_script.py`
负责把朗读稿切成多个 chunk。

职责：
- 按段落或栏目切块
- 控制每块字数上限
- 避免把一句话切断
- 输出 chunk 列表

#### C. `minimax_tts.py`
负责调用 MiniMax TTS。

职责：
- 发送 HTTP 请求
- 处理响应
- 解码音频数据
- 保存 chunk 音频文件
- 记录失败和重试

#### D. `merge_audio.py`
负责把多个 chunk 音频合并成一个最终 mp3。

职责：
- 校验 chunk 文件顺序
- 可选插入短停顿
- 用 ffmpeg 或音频库拼接
- 输出最终音频

#### E. `pipeline.py`
负责把整个日报转音频流程串起来。

职责：
- 读取日报
- 生成 script
- chunk
- TTS
- merge
- 输出 metadata

---

## 7. 错误与风控点

### 1. 英文名 / repo 名发音很怪
解决：
- 第一版先做常见词替换表
- 后续再用 `pronunciation_dict`

### 2. 一整段太长导致失败或效果变差
解决：
- 统一走 chunk
- 单段长度做硬限制

### 3. markdown 看着正常，读出来很别扭
解决：
- 不直接读 markdown
- 必须先生成朗读稿

### 4. 每天同一栏目时长不稳定
解决：
- 记录每 chunk 字数与时长
- 后续可按时长预算反向裁剪内容

### 5. 接口成功但音质/听感不满意
解决：
- 保留 provider 抽象
- 后续可平移到火山引擎/腾讯云做 A/B

---

## 8. 建议的迭代顺序

### V1
- 固定一个官方音色
- 分 chunk 合成
- 拼成一个 mp3
- 只支持中文日报正文

### V2
- 增加 pronunciation 词典
- 增加片头片尾
- 增加 section 间轻停顿
- 增加 metadata 和重试机制

### V3
- 尝试 MiniMax 异步长文本语音合成
- 评估是否可以减少 chunk 数
- 引入固定“日报主播”音色

### V4
- 尝试音色复刻 / 声音设计
- 做更像播客的读稿结构
- 支持 Feishu / 播客 feed / 本地播放器分发

---

## 9. 我当前的推荐结论

如果现在就要开始做，我建议：

1. **先接 MiniMax 同步 HTTP TTS**
2. **先做 markdown -> 朗读稿 -> chunk -> mp3 拼接**
3. **先别上长文本异步和复刻**
4. **先拿最近 3 份日报做样音对比**

优先验证的不是“API 能不能调通”，而是：

- 中文是否自然
- repo / 产品名是否难听
- 一份日报听下来是否像“可听内容”而不是“文档朗读”

只要这三点过线，再继续往产品化升级。

---

## 10. 下一步最小验证任务

### 任务 1：打样
选 1 篇已有日报，手工清洗成 2~3 分钟朗读稿，用 MiniMax 生成样音。

### 任务 2：脚本化
写一个最小脚本：

```text
input: report.md
output: report.mp3
```

### 任务 3：A/B 对比
同一篇日报分别比较：

- 原始 markdown 直接读
- 清洗后的朗读稿再读

只要听一遍，通常就能明显判断“预处理值不值”。
