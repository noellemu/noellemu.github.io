---
title: 【LeetCode HOT 100】34. Find First and Last Position of Element in Sorted Array
date: 2022-11-26 01:54:07
tags:
- Algorithm
- Binary Search
categories:
- Algorithm
- LeetCode
---

## 题目地址

[34. Find First and Last Position of Element in Sorted Array](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

## 解题方法

看到 O(log n) 的时间复杂度限制第一反应二分查找。

最简单的暴力方法（时间复杂度 O(n)）相信大家都会，但是两次二分查找这一方法也不难想到——第一次查找 `target` 第一次出现的位置，第二次查找 `target + 1` 第一次出现的位置，它的前一个位置就是 `target` 最后出现的位置。

在查找 `target` 第一次出现的位置时，查找完毕后需要特殊判断（二分查找左边界 `l` 是否越界，或者 `nums[l]` 是否为 `target`），如果不符合上述判断条件则认为 `target` 在数组中不存在。查找最后出现的位置时则不需要，因为此时 `target` 肯定在数组中，而 `target + 1` 可能本来就不在数组中，或者查找到的位置是数组末尾，这两种情况下查找得到的右边界都是正确的。

## 复杂度分析

时间复杂度：O(log n)，为二分查找算法的时间复杂度。

空间复杂度：O(1)，只使用了常数个数的存储空间。

## AC 代码

```go
func searchRange(nums []int, target int) []int {
    left, right := -1, -1
    // 查找左边界
    l, r := 0, len(nums)-1
    for l <= r {
        m := l + ((r-l)>>1)
        if nums[m] < target {
            l = m + 1
        } else { // m >= target
            r = m - 1
        }
    }
    if l > len(nums)-1 || nums[l] != target {
        return []int{-1, -1}
    }
    left = l
    // 查找右边界
    l, r = 0, len(nums)-1
    for l <= r {
        m := l + ((r-l)>>1)
        if nums[m] < target + 1 {
            l = m + 1
        } else { // m >= target + 1
            r = m - 1
        }
    }
    right = l - 1
    return []int{left, right}
}
```
