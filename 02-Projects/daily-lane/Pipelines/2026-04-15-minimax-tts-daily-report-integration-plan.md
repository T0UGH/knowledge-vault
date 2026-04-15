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

## 6. 工程实现建议

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
