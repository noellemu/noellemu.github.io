---
title: 【LeetCode HOT 100】31. Next Permutation
date: 2022-11-26 03:59:26
tags:
- Algorithm
- Simulation
categories:
- Algorithm
- LeetCode
---

## 题目地址

[31. Next Permutation](https://leetcode.cn/problems/next-permutation/)

## 解题方法

这道题是一个既有的算法，维基百科地址：[Permutation - Wikipedia](https://en.wikipedia.org/wiki/Permutation#Generation_in_lexicographic_order)，原文如下：

> The following algorithm generates the next permutation lexicographically after a given permutation. It changes the given permutation in-place.
>
> 1. Find the largest index k such that `a[k] < a[k + 1]`. If no such index exists, the permutation is the last permutation.
> 2. Find the largest index l greater than k such that `a[k]` < `a[l]`.
> 3. Swap the value of `a[k]` with that of `a[l]`.
> 4. Reverse the sequence from `a[k + 1]` up to and including the final element `a[n]`.

翻译过来就是：

> 以下算法给出了生成某个全排列的字典序中的下一个全排列的方法，该算法是原地工作的。
> 
> 1. 找到最大的满足 `a[k] < a[k + 1]` 的下标 `k`（也就是最后一个升序序列的起点），如果找不到，则该全排列就是最后一个全排列（即整个数组为一个降序序列。按题目要求，我们要翻转整个数组）。
> 2. 找到最大的满足 `a[k] < a[l]` 的下标 `l`（也就是从下标 `k` 开始比 `a[k]` 大的第一个数）。
> 3. 交换 `a[k]` 和 `a[l]` 的值。
> 4. 将数组从 `a[k + 1]` 开始到数组末尾的部分翻转（此时这部分是一个升序序列）。

按照这个算法实现即可。这里我用 `i` 和 `j` 代表算法描述中的 `k` 和 `k + 1`，用 `k` 代表 `l`，并且从后往前遍历数组。

## 复杂度分析

时间复杂度：O(n)，需要遍历数组三次，前两次找到 `i` 和 `j`，第三次翻转 `nums[j:]`。

空间复杂度：O(1)，只使用了常数个数的存储空间。

## AC 代码

```go
func nextPermutation(nums []int)  {
    if len(nums) < 2 {
        return
    }

    // 找到最后一个升序对 (i, j)
    n := len(nums)
    i, j := n - 2, n - 1
    k := n - 1
    for i >= 0 {
        if nums[i] < nums[j] {
            break
        }
        i--
        j--
    }

    // 如果 i < 0，说明整个数组都是降序的，跳过后续步骤直接翻转整个数组
    if i >= 0 {
        // 找到第一个比 i 大的值 k
        for nums[i] >= nums[k] {
            k--
        }
        // 交换 nums[i] 和 nums[k]
        nums[i], nums[k] = nums[k], nums[i]
    }

    // 反转 nums[j:]
    for a, b := j, n-1; a < b; a, b = a+1, b-1 {
        nums[a], nums[b] = nums[b], nums[a]
    }
}
```
