---
title: 【LeetCode HOT 100】11. Container With Most Water
date: 2022-11-25 21:45:39
tags:
- Algorithm
- Two Pointers
categories:
- Algorithm
- LeetCode
---

## 题目地址

[11. Container With Most Water](https://leetcode.cn/problems/container-with-most-water/)

## 解题方法

不难想到我们每次都需要向内移动两侧的板来计算可能达到的最大容量，并且在这个移动过程中维护最大容量，那么如何移动就成了一个问题。如果想让容量尽可能的大，那么我们就需要每次都移动两侧较短的板（如果长度相同的话则移动哪个都可以），直到容器的宽度为 0，这样问题就转换为了一个很简单的双指针问题。

个人认为其实这道题与其说是考察什么算法，不如说是一道博弈论的题目。

## 复杂度分析

时间复杂度：O(n)，需要遍历数组一次。

空间复杂度：O(1)，只使用了常数个数的存储空间。

## AC 代码

```go
func maxArea(height []int) int {
    maxAmount := 0
    l, r := 0, len(height)-1
    for l < r {
        // 求出两侧较短的板的高度
        h := height[l]
        if height[l] > height[r] {
            h = height[r]
        }
        // 求出此时容器的容量，并维护最大容量
        amount := (r - l) * h
        if amount > maxAmount {
            maxAmount = amount
        }
        // 移动较短的一侧
        if height[l] < height[r] {
            l++
        } else {
            r--
        }
    }
    return maxAmount
}
```
