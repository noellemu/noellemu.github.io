---
title: 【HackerRank 1 Week Preparation Kit】01-Plus Minus
date: 2022-11-27 04:14:59
tags:
- Algorithm
- Simulation
categories:
- Algorithm
- HackerRank
---

## 前言

发现了一个很好玩的英文 OJ：HackerRank，听说在国外蛮火的。这个 OJ 支持 Golang，题目是全英文，自带的编辑器有代码提示和自动补全功能体验非常好（LeetCode 的智能模式至今没支持 Golang。。。），也有一些刷题计划可以选择，正好拿来练练英文阅读能力。

而且刷了这么久的 LeetCode，真的是被核心代码模式惯坏了，对 ACM 模式一无所知，完全不会读输入输出，是时候找几个 ACM 模式的 OJ 练练手了。

## 题目原文

Given an array of integers, calculate the ratios of its elements that are positive, negative, and zero. Print the decimal value of each fraction on a new line with `6` places after the decimal.

Note: This challenge introduces precision problems. The test cases are scaled to six decimal places, though answers with absolute error of up to `10^-4` are acceptable.

**Example**

`arr = [1, 1, 0, -1, -1]`

There are  elements, two positive, two negative and one zero. Their ratios are `2/5 = 0.400000`, `2/5 = 0.400000` and `1/5 = 0.200000`. Results are printed as:

> 0.400000
> 0.400000
> 0.200000

**Function Description**

Complete the plusMinus function in the editor below.

plusMinus has the following parameter(s):

int arr[n]: an array of integers
Print
Print the ratios of positive, negative and zero values in the array. Each value should be printed on a separate line with 6 digits after the decimal. The function should not return a value.

**Input Format**

The first line contains an integer, `n`, the size of the array.
The second line contains `n` space-separated integers that describe `arr[n]`.

**Constraints**

- `0 < n <= 100`
- `-100 <= arr[i] <= 100`

**Output Format**

Print the following `3` lines, each to `6` decimals:

1. proportion of positive values
2. proportion of negative values
3. proportion of zeros

**Sample Input**

> STDIN           Function
> -----           --------
> 6               arr[] size n = 6
> -4 3 -9 0 4 1   arr = [-4, 3, -9, 0, 4, 1]

**Sample Output**

> 0.500000
> 0.333333
> 0.166667

**Explanation**

There are `3` positive numbers, `2` negative numbers, and `1` zero in the array.
The proportions of occurrence are positive: `3/6 = 0.500000`, negative: `2/6 = 0.333333` and zeros: `1/6 = 0.166667`.

## 难度

Easy

## 解题思路

入门题，题目的大概含义就是求出 `arr` 中正数、负数和零在数组中的比重，分别统计然后除以数组长度并按要求打印出来即可，注意类型转换。

## 复杂度分析

时间复杂度：O(n)，需要遍历数组一次。

空间复杂度：O(1)，只使用了常数个数的存储空间。

## AC 代码

```go
func plusMinus(arr []int32) {
    // Write your code here
    n := float64(len(arr))
    negatives, positives, zeros := 0.0, 0.0, 0.0
    for _, num := range arr {
        if num < 0 {
            negatives++
        } else if num > 0 {
            positives++
        } else {
            zeros++
        }
    }
    fmt.Printf("%.6f\n%.6f\n%.6f\n", positives / n, negatives / n, zeros / n)
}
```
