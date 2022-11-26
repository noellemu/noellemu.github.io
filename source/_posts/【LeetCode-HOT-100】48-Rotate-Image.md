---
title: 【LeetCode HOT 100】48. Rotate Image
date: 2022-11-26 01:30:54
tags:
- Algorithm
- Brain Tester
categories:
- Algorithm
- LeetCode
---

## 题目地址

[48. Rotate Image](https://leetcode.cn/problems/rotate-image/)

## 解题方法

这道题与其说是一道算法题，不如说是一道脑筋急转弯（严格意义上说，应该是道数学题，详见官方题解）。

在空间复杂度为 O(1) 的限制条件下，比较容易想到的方法是从内向外依次旋转四条边，但是这个方法实现起来比较复杂，也比较容易出错。

其实顺时针旋转 90˚ 最简单的方法是先沿主对角线翻转（也就是旋转 270˚），然后再水平翻转。同理，旋转其他 90˚ 倍数的情况也可以转换为先沿对角线翻转，再水平或垂直翻转的情况。

## 复杂度分析：

时间复杂度：O(n ^ 2)，需要两次遍历，每次都会遍历矩阵中一半的元素。

空间复杂度：O(1)，只使用了常数个数的存储空间。

## AC 代码

```go
func rotate(matrix [][]int) {
    // 沿对角线旋转 270 度
    n := len(matrix)
    for i := 0; i < n; i++ {
        for j := 0; j < i; j++ {
            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
        }
    }
    // 再水平翻转一次
    for i := 0; i < n; i++ {
        for j := 0; j < n / 2; j++ {
            matrix[i][n-j-1], matrix[i][j] = matrix[i][j], matrix[i][n-j-1]
        }
    }
}
```
