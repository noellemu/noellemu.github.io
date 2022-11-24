---
title: Go 语言中的 make 函数
date: 2022-11-25 00:13:56
tags:
- Golang
categories:
- Golang
- Interview
---

## 背景

某一天在阅读公众号时，遇到了这样一道问题：

```go
package main

import "fmt"

// 以下代码输出什么？
func main() {
    m1 := make(map[string]bool, 3)
    m2 := make(map[string]bool)
    fmt.Printf("%d %d", len(m1), len(m2))
    m1["a"] = true
    m2["a"] = true
    m1["b"] = true
    m2["b"] = true
    fmt.Printf("%d %d", len(m1), len(m2))
}
```

答案是 `0 0` 和 `2 2`。

## make() 函数对不同数据类型的作用

这道问题涉及到了关于 `make` 函数的一个知识点：`make` 函数用于初始化某些数据类型时，其参数具有哪些意义？
首先，`make` 函数可以用于初始化三种数据类型：`slice`、`map`、`channel`。我们比较熟悉的用法一般是用于在初始化 `slice` 的时候指定长度（例如 `make([]int, 10)` 可以初始化一个初始长度为 10 的 `int` 类型切片）和在初始化 `channel` 的时候指定该 `channel` 有无缓冲（例如 `make(chan int, 1)` 可以初始化一个缓冲区大小为 1 的 `int` 类型的 `channel`）。
但是一般情况下，当 `make` 用于初始化 `map` 时，一般都只有类型这一个参数，例如 `make(map[int]bool)`。那么第二个参数对于 `map` 来说意味着什么呢？
下面这张表格会告诉我们答案：

| 调用方式                         | 参数类型    | 描述                   |
| ---------------------------- | ------- | -------------------- |
| make(T, len)                 | slice   | 初始化一个长度为 len，容量为 cap 的 slice |
| make(T, len, cap)            | slice   | 初始化一个长度为 len 的 slice            |
| make(T)                      | map     | 初始化一个 map                          |
| make(T, cap)                 | map     | 初始化一个容量**大约**为 cap 的 map      |
| make(T)                      | channel | 初始化一个无缓冲的 channel              |
| make(T, cap)                 | channel | 初始化一个缓冲区大小为 cap 的 channel    |

因此，上面那道题目的答案就显而易见了：`len` 返回的是 `map` 中键值对的个数，而 `make(map[int]bool, 3)` 初始化了一个长度为 0、容量为 3 的 `map`，其初始长度自然就是 0 了。
