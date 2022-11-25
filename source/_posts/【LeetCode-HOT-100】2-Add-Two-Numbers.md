---
title: 【LeetCode HOT 100】2. Add Two Numbers
date: 2022-11-25 20:14:28
tags:
- Algorithm
- Linked List
categories:
- Algorithm
- LeetCode
---

## 题目地址

[2. Add Two Numbers](https://leetcode.cn/problems/add-two-numbers/)

## 解题方法

这道题比较简单，使用一个变量 `carry` 记录进位信息，然后边遍历边相加边判断进位即可，结果可以写入一个新的链表 `ans`。

设 `l1` 当前遍历到的结点为 `p1`，`l2` 当前遍历到的结点为 `p2`，则 `carry` 为：`(p1.Val + p2.Val) / 10`，`ans` 链表该位置上的结点 `p` 的值为 `(p1.Val + p2.Val) % 10`。

当较短的链表遍历完成之后，对于较长的链表，可以有以下两种处理方法：

1. 分情况讨论，对较长的链表继续与 `carry` 执行相加操作，比较容易想到但是代码比较复杂。
2. 在较短的链表后面补 0（`p1` 或 `p2` 为 `nil` 时，将这个结点的值视为 `0`）并使用短链表没有遍历完成时的相加逻辑（代码比较简单）。

两个链表都遍历完成之后，如果 `carry == 1`，则需要在 `ans` 的最后补上一个 `1`。

## 复杂度分析

时间复杂度：O(max(m, n))，其中 m，n 为两个链表的长度。

空间复杂度：O(1)，除返回值外只使用了常数个数的空间。

## AC 代码

分类讨论：

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func addTwoNumbers(l1 *ListNode, l2 *ListNode) *ListNode {
    carry := 0
    p1, p2 := l1, l2
    ans := &ListNode{Val: 0, Next: nil}
    p := ans
    for ; p1 != nil && p2 != nil; p1, p2 = p1.Next, p2.Next {
        val := p1.Val + p2.Val + carry
        carry = val / 10
        val %= 10
        node := &ListNode{Val: val, Next: nil}
        p.Next = node
        p = p.Next
    }
    for ; p1 != nil; p1 = p1.Next {
        val := p1.Val + carry
        carry = val / 10
        val %= 10
        node := &ListNode{Val: val, Next: nil}
        p.Next = node
        p = p.Next
    }
    for ; p2 != nil; p2 = p2.Next {
        val := p2.Val + carry
        carry = val / 10
        val %= 10
        node := &ListNode{Val: val, Next: nil}
        p.Next = node
        p = p.Next
    }
    if carry == 1 {
        p.Next = &ListNode{Val: 1, Next: nil}
    }
    return ans.Next
}
```

补 0:

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func addTwoNumbers(l1 *ListNode, l2 *ListNode) *ListNode {
    carry := 0
    p1, p2 := l1, l2
    ans := &ListNode{Val: 0, Next: nil}
    p := ans
    for p1 != nil || p2 != nil {
        val1, val2 := 0, 0
        if p1 != nil {
            val1 = p1.Val
        }
        if p2 != nil {
            val2 = p2.Val
        }

        val := val1 + val2 + carry
        carry = val / 10
        val %= 10
        node := &ListNode{Val: val, Next: nil}
        p.Next = node
        p = p.Next

        if p1 != nil {
            p1 = p1.Next
        }
        if p2 != nil {
            p2 = p2.Next
        }
    }
    if carry == 1 {
        p.Next = &ListNode{Val: 1, Next: nil}
    }
    return ans.Next
}
```
