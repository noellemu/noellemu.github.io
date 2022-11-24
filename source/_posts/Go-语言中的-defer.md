---
title: Go 语言中的 defer
date: 2022-11-25 00:30:21
tags:
- Golang
categories:
- Golang
- Interview
---

## defer 的作用

`defer` 是 Go 语言提供的一种延迟执行的机制，可以让函数或者语句在当前函数返回（`panic` 或者 `return`）之后执行，且在 `panic` 之后仍然有效，也就是先 `return` / `panic`，后 `defer`。

`defer` 常见的用法是关闭文件、关闭连接、释放资源等，但也可以用来执行一些在不知道是否会 `panic` 的情况下必须执行的操作。比如在项目中，有一个向服务端汇报执行日志的函数 `SendMsg()`，并且在汇报日志之前需要先做一些处理（例如将日志写到文件中等），在发生丢日志的操作之后发现这些处理逻辑可能产生 `panic`，这时候就可以使用 `defer` 来完成汇报日志的操作，确保无论处理逻辑是否 `panic`，日志都会被正常地上报。

此外，使用 `defer` 配合 `recover` 进行 `panic` 兜底并完成一些“收尾操作”（如关闭连接、关闭文件等）也是很常见的操作（实际上 `recover` 在正常情况下只能配合 `defer` 和匿名函数一起使用，直接执行只会返回 `nil`）。例如：

```go
defer func() {
    if err := recover(); err != nil {
        // handle error
    }
}()
```

## defer 的执行顺序

`defer` 遵循栈“先进后出”的特点，也就是先定义的 `defer` 后执行。

## defer 的参数

`defer` 的函数参数会在 `defer` 定义时被保存下来，如果是值类型的参数，则 `defer` 实际执行时与 `defer` 定义时一致；如果是引用类型的参数，则不一定一致（因为指针指向的内容可能会被修改）。

也就是说，如果 `defer` 后跟的语句不是闭包（匿名函数），那么在 `defer` 在定义时，参数就已经确定了。如果是闭包，则会在实际执行时根据闭包的上下文来确定。

因此，如果 `defer` 的参数中含有函数调用，那么在 `defer` 定义处需要先执行这个函数以获取 `defer` 函数的参数，之后 `defer` 会正常执行。

有时候我们可以利用这样的特性确保 `defer` 可以做正确的事情，例如一个进程要循环发起多个网络请求并获取响应，我们就可以利用 `defer` 的特点确保所有的响应体都被正确关闭，例如：

```go
for _, url := range urls {
    resp, err := http.Get(url)
    if err != nil {
        return err
    }
    defer resp.Body.Close()
}
```

这里由于 `resp.Body.Close()` 不是闭包，所以在 `defer` 语句处 `resp.Body` 的值已经确定并且被缓存下来，函数返回后实际执行 `defer` 时就不会出现错误了。

## defer 遇上 panic

`defer` 遇上 `panic` 可以分以下几种情况讨论：

1. 如果 `defer` 执行的函数是 `nil`，则会引发 `panic`。
2. 当函数发生 `panic` 时，会开始遍历 `defer` 链表，按照先进后出的顺序执行 `defer`，此时每个 `defer` 都可以捕获到 `panic`。
3. 遇到前文提到的 `recover` “兜底”代码时，`panic` 会被捕获，之后所有的 `defer` 都无法捕获 `panic`。
4. 如果 `defer` 栈中有多个 `panic`，则后面触发的 `panic` 会覆盖掉前面触发的 `panic`，最终仅有最后一个 `panic` 被 `recover` 捕获。
5. 如果遍历完 `defer` 链表都没有遇上 `recover`，则向 `stderr` 抛出 `panic` 信息，并结束进程。

## 使用 defer 修改函数的返回值

由于 Go 语言中的函数返回值是可以命名的，这些命名的返回值会在函数开始时被初始化为对应类型的零值，作用域为整个函数（包括 `defer`），因此可以使用 `defer` 修改这些返回值。例如：

```go
package main

import "fmt"

func main() {
    fmt.Println(doSomething())
}

func doSomething() (retValue int) {
    defer func() {
        retValue++
    }()
    retValue = 1
    return
}
```
