# LeetCode“最长公共前缀”

> 原文：<https://dev.to/takakd/leetcode-longest-common-prefix-44p8>

哦😮我不知道问题编号是不固定的😧

[最长常用前缀](https://leetcode.com/problems/longest-common-prefix/)

```
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        if len(strs) == 0:
            return ''

        # check what is needed length
        min_len = len(strs[0])
        other_strs = strs[1:]
        for s in other_strs:
            len_s = len(s)
            if min_len > len_s:
                min_len = len_s

        # check prefix
        c = ''
        prefix = ''
        for i in range(min_len):
            c = strs[0][i]
            for s in other_strs:
                if c != s[i]:
                    c = ''

            # not longer a prefix
            if not c:
                break

            prefix += c

        return prefix 
```

Enter fullscreen mode Exit fullscreen mode