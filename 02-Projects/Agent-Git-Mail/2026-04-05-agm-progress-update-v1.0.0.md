---
title: AGM 进度更新：1.0.0 已发布
date: 2026-04-05
tags:
  - agent-git-mail
  - progress
  - release
  - npm
status: active
---

# AGM 进度更新：1.0.0 已发布

## 当前结论

`agent-git-mail` 已完成一轮 **1.0 工程收口**，并已正式发布 **npm 1.0.0**。

这次不是新增大功能，而是把已有 MVP 闭环收成一个更清楚、更可信、更一致的 1.0：

- 入口统一
- profile 模型收口
- 旧安装脚本移除
- README / CLI / runtime 文案对齐
- npm 包元信息对齐
- 发布与 Git tag 完成

---

## 本轮已完成事项

### 1. AGM 1.0 收口设计与实现计划

已产出两份仓库内文档：

- `docs/2026-04-05-agm-1.0-polish-design.md`
- `docs/2026-04-05-agm-1.0-polish-implementation-plan.md`

同时也已同步到 knowledge-vault。

### 2. README 重写

README 已完成一轮以 **profile-first onboarding** 为核心的重写，主要变化：

- 去掉旧 shell 安装脚本主入口
- 去掉旧 plugin/install 心智
- 明确 AGM 的定位：git-native agent 异步邮箱系统
- 强调适用于 OpenClaw 类长期在线助理型 agent
- Quickstart 改成 CLI bootstrap 主路径
- 增加验证路径（config / doctor / log / daemon）
- 后续又追加一轮 polish：
  - 移除 `mt / hex` 这类个人化示例
  - 改成 `agent-a / agent-b`
  - 调整章节顺序与说明

### 3. bootstrap/onboarding 收口

已将 CLI `bootstrap` 收成正式主入口：

```bash
agm --profile <name> bootstrap --self-id <id> --self-remote-repo-url <repo>
```

当前策略：

- `--profile` 是运行主体
- `--self-id` 作为显式参数保留
- `--self-local-repo-path` 不再出现在 README 主路径，只保留为高级 override 参数
- bootstrap help 与输出文案改为 profile-first

### 4. 旧 shell wrapper 删除

已删除：

- `scripts/bootstrap.sh`
- `scripts/install-openclaw.sh`

当前 onboarding 真相只保留 CLI 路径，不再维持第二套 shell 安装真相。

### 5. runtime 文案收口

已收掉多处 pre-profile 术语残留，涉及：

- daemon
- remote discovery
- doctor checks

例如不再强调：

- `self.local_repo_path`

而改为：

- derived self repo path
- profile-first 表达

### 6. identity 轻收口

已在 `send` / `reply` 路径中补上最小 identity guard：

- `profile` 是运行主体
- `self.id` 是 profile 内 identity
- `--from` 若与 profile 的 `self.id` 不一致，会直接报错

这是一次轻收口，不是大规模 identity 模型重构。

### 7. `skills/agm-mail/SKILL.md` 收口

已改为 profile-first：

- 所有命令示例都带 `agm --profile <profile> ...`
- 明确 `--profile` 是 runtime identity
- 明确 `--from` 应与 profile 的 `self.id` 一致
- bootstrap / daemon 说明已同步更新

### 8. npm package metadata 收口

已同步更新：

- `packages/agm/package.json` 版本号
- npm description
- `packages/agm/README.md`

确保 npm 页面不再展示旧的安装脚本和旧说明。

---

## 验证结果

本轮反复执行了：

```bash
npm run build
npm test --workspace packages/agm
```

最终结果：

- build 通过
- **13 个 test files 全通过**
- **58 个 tests 全通过**

说明 AGM 当前的 MVP 闭环在收口后仍然稳定可用。

---

## 发布结果

### npm

- 包名：`@t0u9h/agent-git-mail`
- 版本：`1.0.0`
- 状态：已发布成功

校验结果：

```bash
npm view @t0u9h/agent-git-mail version
# => 1.0.0
```

### GitHub

已完成：

- `main` push
- `v1.0.0` tag push

关键提交：

- `d0cf275` — AGM 1.0 onboarding / runtime consistency 收口
- `113e441` — design + implementation plan
- `4e86ae1` — README + SKILL 最后一轮 polish
- `3720cbc` — publish `@t0u9h/agent-git-mail` v1.0.0

---

## 当前仓库状态判断

### 已完成

- AGM MVP 闭环成立
- AGM 1.0 收口完成
- npm 1.0.0 已发布
- 文档、README、bootstrap、skill、package metadata 已基本对齐

### 尚未处理

- 本地有一个未纳入发布提交的 `.env`
- 是否需要再补 release note / changelog 文案，尚未处理
- 是否需要进一步做更彻底的 identity 统一，当前仍可后置

---

## 下一步建议

当前最合理的下一步不是继续加功能，而是：

1. 回家后做一次 **端到端真机测试**
2. 验证：
   - bootstrap
   - daemon
   - send / reply / read / archive
   - activator / host integration
3. 如果真机测试没问题，再决定：
   - 是否补正式 release note
   - 是否继续做更深一层的 identity / DX polish

一句话：

> **现在 AGM 已经可以从“工程收口完成的 1.0”进入“真实使用验证”阶段。**
