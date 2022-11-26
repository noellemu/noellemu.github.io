---
title: 【LeetCode HOT 100】22. Generate Parentheses
date: 2022-11-26 02:52:44
tags:
- Algorithm
- Recursion
categories:
- Algorithm
- LeetCode
---

## 题目地址

[22. Generate Parentheses](https://leetcode.cn/problems/generate-parentheses/)

## 解题方法

全排列问题，且 n 最大只有 8，一眼递归回溯（本题不需要回溯）。

这道题可以从左右括号的个数的角度出发去思考。规定解空间树从根结点到叶子结点的路径为所求的括号序列，根结点为空字符串，每一个结点都由上一个结点放置一个左括号或右括号而来。由于一个合法的括号序列中左括号的个数总是等于右括号的个数，即左右括号都一共有 n 个，并且总是以左括号开始，且该问题只包含小括号无需考虑不同类型的括号匹配是否合法，所以我们可以规定在递归回溯中优先放置左括号，且解空间树当且仅当从根结点到当前结点的路径上的左括号的个数大于等于右括号的个数时，才允许解空间树产生新的结点，即剩余可用的左括号数不能比右括号数多。

将以上思路代入递归回溯算法的模板中即可。

## 复杂度分析

时间复杂度：O((4^n)/√n)，这个问题的时间复杂度分析超出了我的能力范围，详细的分析过程可以去看官方题解。

空间复杂度：O(n)，递归调用栈的最大深度为 2n。

## AC 代码

```go
func generateParenthesis(n int) []string {
    var ans []string
    var dfs func(int, int, string)
    dfs = func(left, right int, s string) {
        // 只允许左括号的个数大于等于右括号的个数，即剩余的左括号个数不能比剩余的右括号个数多
        if left > right {
            return
        }
        // 左括号和右括号的个数都用完了，说明找到了一组解
        // 这里由于 Go 语言中字符串是值类型，并且每次传入的 s 都是新的字符串，所以不需要拷贝
        if left == 0 && right == 0 {
            ans = append(ans, s)
            return
        }
        // 左括号的个数大于 0，则尝试放置左括号
        if left > 0 {
            dfs(left-1, right, s + "(")
        }
        if right > 0 {
            dfs(left, right-1, s + ")")
        }
    }
    dfs(n, n, "")
    return ans
}
```
