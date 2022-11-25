---
title: 【LeetCode HOT 100】5. Longest Palindromic Substring
date: 2022-11-25 21:25:03
tags:
- Algorithm
- Dynamic Programming (DP)
categories:
- Algorithm
- LeetCode
---

## 题目地址

[5. Longest Palindromic Substring](https://leetcode.cn/problems/longest-palindromic-substring/)

## 解题方法

这道题其实也是一道动态规划的题目，并且使用的是一种比较特殊的方法：中心扩展算法。

一个字符串是回文串的条件如下：

1. 串长为 1 时，该字符串是回文串。
2. 串长为 2 时，如果该字符串的两个字符相同，则该字符串是回文串。
3. 串长大于等于 3 时，如果该字符串去掉头尾两个字符仍然是回文串，那么该字符串是回文串。

据此可以推导出状态转换方程：

- `dp[i][i] = true`
- `dp[i][i+1] = (s[i] == s[i+1])`
- `dp[i][j] = dp[i+1][j-1] && s[i] == s[j]`

根据状态转换方程可以得到表达式 `if s[i] == s[j] && (j-i<=2 || dp[i+1][j-1]) { /* s[i][j] 是回文串 */ }`，因为无论有几个字符，回文串的第一个字符和最后一个字符一定都是相同的（也就是 `s[l] == s[r]`），可以提出来当作公共条件。根据这个表达式依次计算并维护最长回文串的长度和边界即可。

## 复杂度分析

时间复杂度：O(n^2)，需要遍历数组 n ^ 2 次。

空间复杂度：O(n^2)，需要一个 n * n 的 DP 数组辅助计算。

## AC 代码

```go
func longestPalindrome(s string) string {
    if len(s) < 2 {
        return s
    }
    n := len(s)
    maxL, maxR := 0, 0
    maxLen := 0
    // 构造一个 n * n 的数组暂存结果
    dp := make([][]bool, n)
    for i := 0; i < n; i++ {
        dp[i] = make([]bool, n)
    }
    // 初始状态：l = r 时，dp[l][r] = true
    // 状态转移方程：当 s[l] = s[r] 且 l - r < 2（子串只有两个以下字符）或 dp[l+1][r-1] = true 时，dp[l][r] = true
    for r := 1; r < len(s); r++ {
        for l := 0; l < r; l++ {
            if s[l] == s[r] && (r-l<=2 || dp[l+1][r-1]) {
                dp[l][r] = true
                if r - l + 1 > maxLen {
                    maxLen = r - l + 1
                    maxL, maxR = l, r
                }
            }
        }
    }
    // 注意 Golang 的截取操作为左闭右开
    return s[maxL:maxR+1]
}
```
