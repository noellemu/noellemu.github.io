---
title: 【LeetCode HOT 100】3. Longest Substring Without Repeating Characters
date: 2022-11-25 20:50:22
tags:
- Algorithm
- Hash Map
- Sliding Window Algorithm
categories:
- Algorithm
- LeetCode
---

## 题目地址

[3. Longest Substring Without Repeating Characters](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

## 解题方法

这道题可以说是滑窗的入门题了。

用 `l` 和 `r` 两个变量规定滑窗的左右两端，`l` 每次循环前进一个字符，并把离开滑窗的字符 `s[l-1]` 从滑窗中移除。`l` 前进完毕后，开始尝试扩张滑窗：`r` 每次前进一个字符，并把进入滑窗的字符 `s[r]` 添加到哈希表中，直到 `r` 越界或 `s[r]` 在哈希表中出现时，停止扩张，计算滑窗长度 `r - l` 并更新滑窗的最大长度 `ans`，最后返回 `ans` 即可。

## 复杂度分析

时间复杂度：O(n)，需要将 `s` 遍历一次。

空间复杂度：O(|∑|)，题目规定“s consists of English letters, digits, symbols and spaces”，即 ASCII 字符集，因此可以认为最坏情况为 ASCII 字符集中的所有字符都出现在滑窗中，所以 ∑ 为 ASCII 字符集的长度，即 `128`。

## AC 代码

```go
func lengthOfLongestSubstring(s string) int {
    m := make(map[byte]bool)
    ans, r := 0, 0
    for l := 0; l < len(s); l++ {
        // 从哈希表里删除离开滑窗的字符
        if l > 0 {
            delete(m, s[l-1])
        }
        // 开始尝试向右扩张滑窗，直到越界或遇到滑窗中已有的字符
        // 每次将一个字符纳入滑窗，都将它放入哈希表中
        for ; r < len(s) && !m[s[r]]; r++ {
            m[s[r]] = true
        }
        // 向右扩张完毕，计算出滑窗当前的长度 r - l，并与最大值比较
        if r - l > ans {
            ans = r - l
        }
    }
    return ans
}
```
