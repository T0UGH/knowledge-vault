# AGM Feishu system message handling gap

## 背景

在 Feishu DM 中，AGM 新信到达会以系统消息形式出现，例如：

`System: [2026-04-02 10:24:13 GMT+8] New agent git mail: from=mt, file=2026-04-02T02-23-55Z-mt-to-leo-b0c7.md`

Leo 能正确把这类内容识别为 **system message / 环境事件**，而不是用户消息。

## 暴露出的问题

虽然 Leo 知道“收到了一封新信”，但第一次处理时没有立刻进入正确的读取路径，而是先去文件系统里盲找正文文件。

实际更合理的处理应该是：

1. 识别这是 AGM 的系统通知
2. 直接使用 `agm` CLI
3. 优先执行：
   - `agm list --agent leo --dir inbox --format table`
   - `agm read <filename> --agent leo --dir inbox`

## 问题本质

这不是“Leo 把系统消息忽略了”，而是：

- **决策上认为应该看信**
- **执行上没有立刻命中 AGM 作为专用入口**

也就是说，问题是 **tool routing / action routing 错位**，不是消息分类错误。

## 影响

会导致：

- 收到 AGM 通知后，不能第一时间读到正文
- 增加一次无效排查
- 给人感觉像“看见了系统通知，但没真正处理”

## 建议修复方向

建议 MT 侧检查或补强以下任一层：

1. **提示词层**
   - 当出现 `New agent git mail: from=..., file=...` 时，明确提醒应优先使用 `agm list/read`

2. **工具发现层**
   - 让 agent 更容易将 `agent git mail` 与 `agm` CLI 自动关联起来

3. **运行时桥接层**
   - 若能在系统通知中附带更明确的 action hint，会减少误路由

## 这次验证结论

本次链路是通的：

- Leo 确实收到了 AGM 的系统通知
- 也能通过 `agm` 成功读到正文
- 当前问题主要是 **首次响应路径不够短、不够稳**
