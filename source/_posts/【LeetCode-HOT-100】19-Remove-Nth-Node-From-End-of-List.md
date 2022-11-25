---
title: 【LeetCode HOT 100】19. Remove Nth Node From End of List
date: 2022-11-25 22:34:53
tags:
- Algorithm
- Two Pointers
- Linked List
categories:
- Algorithm
- LeetCode
---

## 题目地址

[19. Remove Nth Node From End of List](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

## 解题方法

看到链表第一反应：双指针。

求链表长度等比较简单的方法就不讲了，这里讲一下最优解（双指针）。

这道题如果是一个有头结点的链表，那么能简化很多代码，我们可以给链表手动添加一个头结点 `dummy`。初始情况下快指针 `f` 指向第一个结点 `head`，慢指针 `s` 指向头结点 `dummy`。由于需要求倒数第 n 个结点，那么可以让快指针 `f` 先走 n 步，然后快慢指针一起走，直到 `f` 越界为止，此时 `s` 指向的就是倒数第 `n` 个结点的前一个结点，直接把 `s` 的下一个结点从链表中摘除（`s.Next = s.Next.Next`）即可。注意最后需要返回 `dummy.Next`，否则链表中只有一个结点的情况会 WA（也可以使用 `head.Next == nil` 特殊判断一下）。

## 复杂度分析

时间复杂度：O(n)，需要完整遍历一次链表。

空间复杂度：O(1)，只使用了常数个数的存储空间。

## AC 代码

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func removeNthFromEnd(head *ListNode, n int) *ListNode {
    dummy := &ListNode{Val: -1, Next: head}
    f, s := head, dummy
    for i := 0; i < n; i++ {
        f = f.Next
    }
    for f != nil {
        f = f.Next
        s = s.Next
    }
    s.Next = s.Next.Next

    return dummy.Next
}
```
