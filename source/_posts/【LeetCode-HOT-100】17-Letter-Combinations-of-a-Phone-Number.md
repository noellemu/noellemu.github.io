---
title: 【LeetCode HOT 100】17. Letter Combinations of a Phone Number
date: 2022-11-26 02:16:16
tags:
- Algorithm
- Recursion
categories:
- Algorithm
- LeetCode
---

## 题目地址

[17. Letter Combinations of a Phone Number](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)

## 解题方法

全排列（Combination / Permutation）问题，并且问题规模不大（`digits` 的最大长度为 6），一眼递归回溯（本题不需要回溯）。

递归问题首先从边界条件开始考虑，然后思考能不能转换为更小的问题：

- 0 个数字，不需要任何操作，直接返回（相当于得到一个空字符串）。
- 1 个数字，枚举这个数字所对应的所有字母，相当于这个数字对应的所有字母拼接空字符串。
- 2 个数字，枚举第二个数字对应的所有字母，并与第一个数字对应的所有字母拼接。
- n 个数字，枚举第 n 个数字对应的所有字母，并与第 n - 1 个数字对应的所有结果拼接。

按照以上方法编写递归代码即可。

## 复杂度分析

时间复杂度：O(3^m * 4^n)，其中 m 为输入中对应 3 个字母的数字总数，n 为输入中对应 4 个字母的数字总数。

空间复杂度：O(m + n)，其中 m 为输入中对应 3 个字母的数字总数，n 为输入中对应 4 个字母的数字总数，m + n 为递归调用栈的最大深度。

## AC 代码

```go
var (
    numberMap = map[byte][]string{
        '2': []string{"a", "b", "c"},
        '3': []string{"d", "e", "f"},
        '4': []string{"g", "h", "i"},
        '5': []string{"j", "k", "l"},
        '6': []string{"m", "n", "o"},
        '7': []string{"p", "q", "r", "s"},
        '8': []string{"t", "u", "v"},
        '9': []string{"w", "x", "y", "z"},
    }
)

func letterCombinations(digits string) []string {
    // 因为有 0 个字符时需要返回 [] 而不是 [""]，所以要特殊判断一下
    if len(digits) == 0 {
        return []string{}
    }
    var ans []string
    var dfs func(string, int)
    dfs = func(combination string, nextIndex int) {
        // 遍历到字符串末尾，说明找到了一组解
        if nextIndex == len(digits) {
            ans = append(ans, combination)
            return
        }
        // 否则对当前遍历到的字符集合中的每一个字符都尝试执行递归操作
        for _, s := range numberMap[digits[nextIndex]] {
            dfs(combination + s, nextIndex + 1)
        }
    }
    dfs("", 0)
    return ans
}
```
