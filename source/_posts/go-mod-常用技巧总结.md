---
title: go mod 常用技巧总结
date: 2022-11-09 10:22:00
tags:
  - Go
categories:
  - Go
---

~~遇事不决 go mod tidy~~

## go mod 指定分支

`go get` git 地址@分支名称

```shell
go get -u github.com/xxxx/xxx@branch
```

## go mod replace 将远程包替换为本地包

将远程包 `github.com/noellemu/api` 替换为本地包（支持绝对路径和相对路径）时，`go.mod` 的写法如下：

```go
replace github.com/noellemu/api => ../api
require (
    github.com/noellemu/api v1.0.0
)
```

然后 `go mod tidy` 即可。
