---
title: 【LeetCode HOT 100】15. 3Sum
date: 2022-11-26 02:02:40
tags:
- Algorithm
- Two Pointers
categories:
- Algorithm
- LeetCode
---

## 题目地址

[15. 3Sum](https://leetcode.cn/problems/3sum/)

## 解题思路

最简单的暴力方法（时间复杂度为 O(n ^ 3)）相信大家都能想到，但这个问题的难点在于去除重复解，在暴力方法中，可以使用哈希表来去除。

这道题目的最优解是排序 + 双指针。我们可以先对 `nums` 数组从小到大排序，然后依次遍历每一个负数并跳过重复的数，然后定义 `l`、`r` 两个指针分别指向遍历到的数之后的第一个数和数组的末尾，并计算遍历到的数、`nums[l]`、`nums[r]` 相加是 0、正数还是负数。如果是 0，则找到了一组解；如果是负数，说明 `nums[l]` 太小，将 `l` 向右（更大的数的方向）移动；如果是正数，说明 `nums[r]` 太大，将 `r` 向左（更小的数的方向）移动，直到两个指针相遇，注意在移动过程中需要跳过重复的数。

## 复杂度分析

时间复杂度：O(n ^ 2)，其中排序算法 O(nlog n)，双指针遍历过程 O(n) * O(n) = O(n ^ 2)：对于数组中的每一个数，在最坏情况下双指针都要遍历完整个数组。

空间复杂度：O(1)，只使用了常数个数的存储空间。

## AC 代码

```go
func threeSum(nums []int) [][]int {
    ans := make([][]int, 0)
    // 从小到大排序
    sort.Ints(nums)
    for i := 0; i < len(nums); i++ {
        // 如果第一个元素就大于 0，说明后面不可能存在小于 0 的解了，停止搜索
        if nums[0] > 0 {
            return ans
        }
        // 跳过重复的数
        if i > 0 && nums[i] == nums[i-1] {
            continue
        }
        l, r := i + 1, len(nums) - 1
        for l < r {
            if nums[l] + nums[r] + nums[i] == 0 {
                // 等于 0，找到了一组解
                ans = append(ans, []int{nums[i], nums[l], nums[r]})
                // 跳过重复的数
                for l < r && nums[l] == nums[l+1] {
                    l++
                }
                for l < r && nums[r] == nums[r-1] {
                    r--
                }
            }
            // 如果结果大于 0，尝试移动右指针，否则移动左指针
            if nums[l] + nums[r] + nums[i] > 0 {
                r--
            } else {
                l++
            }
        }
    }
    return ans
}
```
