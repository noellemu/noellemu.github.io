---
title: 【LeetCode HOT 100】46. Permutations
date: 2022-11-26 02:27:42
tags:
- Algorithm
- Recursion
- Backtrack
categories:
- Algorithm
- LeetCode
---

## 题目地址

[46. Permutations](https://leetcode.cn/problems/permutations/solution/)

## 解题方法

全排列问题，并且输入的最大长度只有 6，一眼递归回溯，这道题算是递归回溯的入门题了（不过我个人认为不需要回溯的更适合新手练习，比如 [17](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/) 和 [22](https://leetcode.cn/problems/generate-parentheses/submissions/)）。

先考虑递归的条件：

- `nums` 长度为 1，全排列就是 `nums[0]` 自身。
- `nums` 长度为 2，全排列为 `[[nums[0], nums[1]], [nums[1], nums[0]]]`。
- `nums` 长度为 n，全排列就是 `nums[n]` 和 `nums[0:n-1]` 的全排列。

本题有一个隐含条件：数字不能重复使用，那么如何去重就成为了一个问题。一般在去重类的问题中，除了算法本身可以去重（例如官方题解那种不使用辅助数组的方法）之外，我们可以使用标记数组 `visited` 进行去重。

递归回溯类的问题一般都可以画出一棵树（解空间树），我们用 `path` 保存解空间树从根结点到叶子结点之间的路径。上述递归条件如果画成一棵 n 叉树会更加容易想到，所求的全排列就是根结点到叶子结点中经过的结点值不相同的路径的集合，综合这些条件代入递归回溯算法的模板即可。

## 复杂度分析

时间复杂度：O(n * n!)，其中 n 为 `nums` 的长度。

空间复杂度：O(n)，递归调用栈的最大深度为 n。

## AC 代码

```go
func permute(nums []int) [][]int {
    var ans [][]int
    visited := make([]bool, len(nums))
    var dfs func(int, []int)
    dfs = func(start int, path []int) {
        if start == len(nums) {
            // 这里必须 copy，否则会 WA
            _path := make([]int, len(path))
            copy(_path, path)
            ans = append(ans, _path)
        }
        for i, v := range nums {
            // 只能用 nums 数组中没使用过的数
            if !visited[i] {
                visited[i] = true
                path = append(path, v)
                dfs(start + 1, path)
                path = path[:len(path)-1]
                visited[i] = false
            }
        }
    }
    dfs(0, []int{})
    return ans
}
```
