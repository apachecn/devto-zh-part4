# LeetCode“合并两个排序列表”

> 原文：<https://dev.to/takakd/leetcode-merge-two-sorted-lists-571m>

递归，递归，递归...🤮

**参考**
[https://leet code . com/problems/merge-two-sorted-lists/discuse/381943/python-concise-recursion-code](https://leetcode.com/problems/merge-two-sorted-lists/discuss/381943/python-concise-recursion-code)

```
class Solution:
    def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
        if l1 is None and l2 is None:
            return None
        elif l1 is None:
            return l2
        elif l2 is None:
            return l1

        if l1.val < l2.val:
            l1.next = self.mergeTwoLists(l1.next, l2)
            return l1
        else:
            l2.next = self.mergeTwoLists(l1, l2.next)
            return l2 
```

Enter fullscreen mode Exit fullscreen mode