---
title: Go 语言中空结构体 struct{} 的作用
date: 2022-11-24 12:26:17
tags:
- Golang
---

## struct{} 的特性

在 Go 的源代码中，对 `struct{}` 的内存申请都会返回一个固定的内存地址，这说明 `struct{}` 是不占用内存空间的，同样不占用内存空间的还有 `[]struct{}` 等。

对 `struct{}` 调用 `unsafe.Sizeof()` 得到的结果也是 0。

## 配合 map 构造 set

Go 语言是没有自带 `set` 这种数据结构的，但可以使用 `map` 配合 `struct{}` 构造 `set`，比使用 `bool` 每条数据可以节省一个字节的空间：

```go
// 构造一个 string 类型的 set
set := map[string]struct{}
// 向 set 中添加元素
set["a"] = struct{}{}
// 判断一个元素（key）是否在 set 中
if _, ok := set[key]; ok {
    // 存在
} else {
    // 不存在
}
// 遍历 set
for key := range set {
    // ...
}
```

## 作为方法的 receiver

这种写法比较少见（我在项目中其实还是见过的，只是比较少），但是不会报错：

```go
type CalcService struct{}

func (s XxxService) Add(a, b int) int {
    return a + b
}

func (s XxxService) Mul(a, b int) int {
    return a * b
}
```

## 作为 channel 的类型

有的时候我们声明一个 `channel` 并不需要它发送数据，而是进行 `goroutine` 之间的调度、控制 `goroutine` 的个数等，这时候就可以声明一个 `struct{}` 类型的 `channel`。

例如以下是一个使用 `chan struct{}` 进行 `goroutine` 调度，使得两个 `goroutine` 交替输出 1 - 100 的例子（经典面试题）：

```go
package main

import (
    "fmt"
    "sync"
)

var (
    i      = 1
    chOdd  = make(chan struct{})
    chEven = make(chan struct{})
    wg     = sync.WaitGroup{}
)

// 输出奇数
func odd() {
    defer wg.Done()
    for {
        <-chOdd
        fmt.Println("goroutine1:", i)
        i++
        if i == 100 {
            chEven <- struct{}{}
            return
        }
        chEven <- struct{}{}
    }
}

// 输出偶数
func even() {
    defer wg.Done()
    for {
        <-chEven
        fmt.Println("goroutine2:", i)
        i++
        if i == 101 {
            return
        }
        chOdd <- struct{}{}
    }
}

func main() {
    wg.Add(2)
    go odd()
    go even()
    chOdd <- struct{}{}
    wg.Wait()
}

```
