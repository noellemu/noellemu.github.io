---
title: Windows PowerShell 开启命令历史提示
date: 2022-11-10 13:20:04
tags:
  - Windows
categories: 
  - Windows
---

先看配置效果：

![配置效果](https://cdn.jsdelivr.net/gh/noellemu/pictures/img/20221110133002.png)

## 问题背景

如果你在 Google 上搜索“Windows Powershell 开启命令历史提示”，你会搜到一条这样的命令：`Set-PSReadlineOption -PredictionSource History`，但是这条命令并不是所有人都能直接执行的，例如我，执行这条命令之后会报“Invalid Argument”错误。

出现错误的原因可以参考 [这个 Issue](https://github.com/PowerShell/PSReadLine/issues/3117)，简单来说就是系统自带的 `Set-PSReadLineOption` 版本太低，不支持 `-PredictionSource` 参数，所以我们需要升级 `PSReadLine`。从官方文档中可以查到升级的命令为 `Install-Module PSReadLine -Force -SkipPublisherCheck -AllowPrerelease`，但这条命令也是不可以直接执行的，所以我们需要先升级 `PowerShellGet`，然后再升级 `PSReadLine`。

## 升级 PowerShellGet

用管理员权限打开 `Windows PowerShell`，执行：

```powershell
Install-Module -Name PowerShellGet -Force
```

## 升级 PSReadLine 并配置命令历史提示功能

然后升级 `PSReadLine`，这条命令不需要管理员权限：

```powershell
Install-Module PSReadLine -Force -SkipPublisherCheck -AllowPrerelease
```

升级完成后，编辑配置文件，配置历史提示功能：

```powershell
vim $Profile
# 开启命令历史提示，并设置样式为文章开头图片中的列表样式
Set-PSReadLineOption -ShowToolTips -PredictionSource History
Set-PSReadLineOption -PredictionViewStyle ListView
```

最后重新打开 `PowerShell`，此时输入命令就可以看到历史提示了，并且可以使用上下方向键选择想要执行的命令，可以愉快地玩耍了。
