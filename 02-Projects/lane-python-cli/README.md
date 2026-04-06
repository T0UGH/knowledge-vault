# lane Python CLI（暂定名）

## 这个项目是干什么的

这是从 `daily-lane` 演进出来的新方向项目入口。

当前共识：
- lane 后续定位为 **collect engine**
- 日报生成仍然交给 agent
- collect 层只做好 collect，不承担候选裁剪式决策
- 过滤 / 去重 / 打分 / 排序 属于概念独立的一层，但暂不急着开工
- 运行纪律层（diagnose / check-config / mock / status）是近期优先方向
- 后续准备从 shell-first runtime 迁向 **Python CLI**

## 当前讨论焦点

1. Python CLI 的项目定位与边界
2. 新 CLI runtime 的命令结构
3. 哪些 lane 先迁、哪些后迁
4. collect engine 与 future processing layer 的关系
5. 运行纪律层哪些先做、哪些后做

## 当前迁移难度判断（初稿）

从低到高：
1. `x-feed`
2. `x-following`
3. `github-trending-weekly`
4. `github-watch`
5. `product-hunt-watch`

## 待定事项

- 项目正式名称
- 仓库路径 / repo 名称
- CLI 命令结构
- 首批迁移 lane
- 旧 `daily-lane` 与新项目的过渡方式
