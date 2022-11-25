---
title: 【LeetCode HOT 100】1. Two Sum
date: 2022-11-25 20:00:04
tags:
- Algorithm
- Hash Map
categories:
- Algorithm
- LeetCode
---

## 题目地址

[1. Two Sum](https://leetcode.cn/problems/two-sum/)

## 解题方法

这道题也是老熟人了，我感觉我至少做过五遍。

暴力解法（时间复杂度 `O(n^2)`）大家应该都知道，那就来讲讲非暴力解法吧。

我们可以用一个哈希表 `m` 保存 `nums` 中的元素与下标的对应关系，由于一个数字只能使用一次，因此我们遍历 `nums` 数组，每次都查找 `target` 与当前元素 `num` 的差值是否在哈希表中。如果在，则说明 `m[target-num] + num = target`，返回 `m[target-num]` 和 `num` 的下标 `i1`；如果不在，则将 `num` 及其下标添加到哈希表中（即 `m[num] = i1`）。

## 复杂度分析

时间复杂度：O(n)，需要遍历一次数组。

空间复杂度：O(n)，哈希表占用的内存空间与 `nums` 的长度相关。

## AC 代码

```go
func twoSum(nums []int, target int) []int {
     m := make(map[int]int)
    for i, num := range nums {
        if i1, ok := m[target-num]; !ok {
            m[num] = i
        } else {
            return []int{i1, i}
        }
    }
    return []int{0, 0}
}
```
