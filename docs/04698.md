# LeetCode“从列表末尾删除第 n 个节点”

> 原文：<https://dev.to/takakd/leetcode-remove-nth-node-from-end-of-list-28kg>

我解决了，但这可能是 shorter☹️

[从列表末尾删除第 n 个节点](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None 
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        if n == 0:
            return None
        if n == 1 and head.next is None:
            return None

        i = 1
        before = None
        target = head
        node = head
        while node:
            if n < i:
                before = target
                target = target.next
            i += 1
            node = node.next

        if before is None:
            return head.next

        before.next = target.next    
        return head 
```

Enter fullscreen mode Exit fullscreen mode