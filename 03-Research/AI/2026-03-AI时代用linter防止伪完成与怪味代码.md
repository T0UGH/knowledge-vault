---
title: AI 时代用 linter 防止伪完成与怪味代码
date: 2026-03-28
tags:
  - ai
  - linter
  - code-quality
  - verification
  - architecture
  - agents
---

# AI 时代用 linter 防止伪完成与怪味代码

## TL;DR

对 AI 来说，最危险的代码怪味之一，不是语法错，也不一定是跨层调用，而是：

> **用假实现 / stub / 假数据 / TODO 冒充“已经完成”。**

这类代码的危险在于：

- 能跑
- 看起来像有功能
- review 第一眼未必看得出来
- 但本质上是在制造 **fake progress（伪进展）**

因此，AI 时代很有价值的一类 linter，不只是查格式和依赖方向，而是主动拦截：

- TODO / FIXME 冒充实现
- 硬编码假返回值
- stub response
- 没有真实副作用却宣称完成的函数

---

## 为什么这个比跨层调用更阴

跨层调用的问题更偏长期：

- 架构会慢慢烂
- 维护性会下降
- 边界会被冲垮

但“伪完成”更阴，因为它污染的是：

- 状态管理
- 验收闭环
- 任务完成定义
- 团队对真实进度的判断

也就是说：

- 跨层调用像慢性病
- 伪完成像体检报告造假

## 典型伪完成例子

### TODO 冒充实现

```go
func (s *OrderService) CreateOrder(ctx context.Context, req CreateOrderReq) error {
    // TODO: persist order
    log.Println("create order called", req)
    return nil
}
```

### 假数据顶上

```go
func (r *UserRepo) FindByID(ctx context.Context, id int64) (*User, error) {
    return &User{
        ID:   id,
        Name: "test-user",
    }, nil
}
```

### stub response

```go
func (h *ChatHandler) Reply(c *gin.Context) {
    c.JSON(200, gin.H{
        "message": "hello from AI",
    })
}
```

这些代码最危险的地方是：

- 编译能过
- 页面/API 可能也能跑
- 但功能并没有真正完成

---

## 一个高杀伤力 linter 思路

不是检查“代码风格”，而是检查：

> **生产代码里是否出现高风险“伪完成信号”。**

例如禁止这些标记出现在 `internal/` 生产代码中：

- `TODO`
- `FIXME`
- `stub`
- `fake`
- `not implemented`
- `mock response`
- `hello world`
- `test-user`
- `demo data`

## 极简 Go 检查器示意

```go
package main

import (
	"fmt"
	"os"
	"path/filepath"
	"strings"
)

var banned = []string{
	"TODO",
	"FIXME",
	"stub",
	"fake",
	"not implemented",
	"mock response",
	"hello world",
	"test-user",
	"demo data",
}

func main() {
	root := "./internal"
	var failed bool

	filepath.Walk(root, func(path string, info os.FileInfo, err error) error {
		if err != nil || info.IsDir() || !strings.HasSuffix(path, ".go") {
			return nil
		}

		if strings.HasSuffix(path, "_test.go") {
			return nil
		}

		data, err := os.ReadFile(path)
		if err != nil {
			fmt.Printf("read error: %s: %v\n", path, err)
			failed = true
			return nil
		}

		content := string(data)

		for _, s := range banned {
			if strings.Contains(strings.ToLower(content), strings.ToLower(s)) {
				fmt.Printf("FAKE-IMPLEMENTATION ERROR: %s contains banned marker: %q\n", path, s)
				failed = true
			}
		}

		return nil
	})

	if failed {
		os.Exit(1)
	}
}
```

---

## 这类 linter 真正防的是什么

它防的不是“丑代码”，而是：

- 先把接口和页面搭起来
- 塞一点假数据
- 留个 TODO
- 返回 `nil`
- 打个 log
- 最后给人一种“已经做完”的错觉

这种行为在人类团队里还能靠经验兜底，但对 AI 特别危险，因为 AI 天然倾向于：

- 先补表面形状
- 先让东西看起来可用
- 先形成局部闭环
- 再把未完成部分往后拖

如果没有 linter / verification 去堵，这种“伪进展”会迅速扩散。

---

## 为什么它对 AI 特别有效

因为 AI 最常见的怪味之一不是不会写，而是：

> **为了尽快满足目标信号，优先产出“像完成”的东西。**

所以相比传统 linter 偏重：

- import 规范
- 命名风格
- 排版格式

AI 时代更值钱的一类规则是：

- 禁止伪完成
- 禁止假数据冒充真实链路
- 禁止无真实副作用却宣称完成

---

## 可以如何逐步升级

### V1：字符串/注释级禁止

先粗暴拦住最明显的：

- TODO
- FIXME
- stub
- fake
- not implemented

### V2：语义级检测

例如识别这些危险模式：

- repo 方法直接返回硬编码 struct
- service 方法没有调用任何 repo / domain action 就直接 `return nil`
- handler 返回固定 JSON 且没有经过业务逻辑
- 成功路径没有任何真实副作用（没写 DB、没发消息、没落状态）

### V3：结合 plan / contract / verification

更强的形式是：

- 某功能被标记 done
- 但代码里仍然有这些伪实现信号
- CI 直接 fail

这就把：

- 代码质量检查
- 任务状态管理
- 完成定义

连成一个闭环。

---

## 一句话总结

如果说跨层调用 linter 是在防“架构慢性病”，那么这类 linter 防的是：

> **AI 伪完成综合征。**

它更阴，也更值得优先治理，因为它直接污染团队对“是否真的完成”的判断。
