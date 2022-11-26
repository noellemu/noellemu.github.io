---
title: 【LeetCode HOT 100】33. Search in Rotated Sorted Array
date: 2022-11-26 01:39:04
tags:
- Algorithm
- Binary Search
categories:
- Algorithm
- LeetCode
---

## 题目地址

[33. Search in Rotated Sorted Array](https://leetcode.cn/problems/search-in-rotated-sorted-array/)

## 解题方法

看到 O(log n) 的时间复杂度第一反应二分查找。

这道题需要分情况讨论，一图胜千言：

![Binary Search](https://pic.leetcode.cn/1669485127-hCyVUY-IMG_33F9763FE2FE-1.jpeg)

解释（设 `m` 为本次二分查找的中心点）：

- 当数组的左半部分不包含旋转部分时，`target` 在左半部分的条件为：`nums[0] <= target < nums[m]`。
- 当数组的左半部分包含旋转部分时，有两种情况：
  - 1. `target` 不在旋转部分，此时条件为：`nums[m] < nums[0] <= target`。
  - 2. `target` 在旋转部分，此时条件为：`nums[m] <= target < nums[0]`。

对于以上三种情况，都可以认为 `target` 在数组的左半部分，否则在数组的右半部分，按照这个条件代入到二分查找的代码中即可。

## 复杂度分析

时间复杂度：O(log n)，为二分查找算法的时间复杂度。

空间复杂度：O(1)，只使用了常数个数的存储空间。

## AC 代码

其实查询条件可以优化，现在这样写有点长，而且如果代码高亮没有括号着色功能的话容易看懵。

```go
func search(nums []int, target int) int {
    n := len(nums)
    l, r := 0, n-1
    for l <= r {
        m := l + ((r-l)>>1)
        if nums[m] == target {
            return m
        }
        if (nums[0]<nums[m] && nums[0]<=target && target<nums[m]) || (nums[0]>nums[m] && (nums[m]<nums[0] && nums[0]<=target) || (target<=nums[m] && nums[m]<nums[0])) {
            r = m - 1
        } else {
            l = m + 1
        }
    }
    return -1
}
```
