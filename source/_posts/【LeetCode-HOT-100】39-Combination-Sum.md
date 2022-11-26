---
title: 【LeetCode HOT 100】39. Combination Sum
date: 2022-11-26 03:45:24
tags:
- Algorithm
- Recursion
categories:
- Algorithm
- LeetCode
---

## 题目地址

[39. Combination Sum](https://leetcode.cn/problems/combination-sum/)

## 解题方法

全排列问题，一眼递归回溯。

这个问题的求解过程有点类似于 [46](https://leetcode.cn/problems/permutations/solution/)，都需要使用类似于枚举输入数组 `candidates` 全排列的思路，不过这道题的 `candidates` 数组中的元素是可以重复使用的，因此每次递归时都需要从当前结点开始。此外，为避免重复解，在每次递归遍历 `candidates` 数组时需要从当前搜索位置 `start` 开始遍历（当然你也可以考虑使用 `visited` 标记数组之类的方法来去重），每次从 `target` 中减掉当前遍历到的元素，直到 `target` 等于 0 时达到递归边界，此时根结点到该叶子结点的路径即为一组解。

由于输入规模在递归回溯类问题中比较大，所以可以考虑对解空间树剪枝。先对 `candidates` 从小到大排序，当遍历到解空间树的某个结点时，如果 `target` 减掉要遍历的下一个元素的值为负数，说明这个值和之后所有的值都不能满足要求，直接 `break` 即可。

## 复杂度分析

时间复杂度：O(S)，其中 S 为所有可行解的长度之和。

空间复杂度：O(target)，最坏情况即 `candidates` 为 `[1]` 时，递归调用栈的最大深度为 `target`。

## AC 代码

```go
func combinationSum(candidates []int, target int) [][]int {
    ans := make([][]int, 0)
    path := make([]int, 0)
    sort.Ints(candidates)
    var dfs func(int, int)
    dfs = func(start, target int) {
        // 递归边界：target 为 0，表示找到了一组解
        if target == 0 {
            _path := make([]int, len(path))
            copy(_path, path)
            ans = append(ans, _path)
            return
        }
        // 依次遍历剩余的数字，避免结果重复
        for i := start; i < len(candidates); i++ {
            // 由于前面已经对 candidates 排过序了，所以如果 target - candidates[i] 是负数，可以直接跳过后面的数
            if target - candidates[i] < 0 {
                break
            }
            path = append(path, candidates[i])
            // 由于数字可以重复使用，所以这里的搜索起点仍然是当前位置
            dfs(i, target - candidates[i])
            path = path[:len(path)-1]
        }
    }
    dfs(0, target)
    return ans
}
```
