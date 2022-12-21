# LeetCode“有效括号”

> 原文：<https://dev.to/takakd/leetcode-valid-parentheses-m8a>

我经常被计数器的索引搞糊涂🙄

[有效括号]('https://leetcode.com/problems/valid-parentheses/')

```
class Solution:
    def isValid(self, s: str) -> bool:

        len_s = len(s)

        if len_s == 0:
            return True
        elif len_s < 2:
            return False

        close_parentheses = {'(': ')', '{': '}', '[': ']'}

        # @note: it's faster to start with i = 1, but i = 0 and check i + 1 is easier to understand.
        i = 0
        while (i + 1) < len_s:
            c = s[i]
            if (c == '(' or c == '{' or c == '[') and s[i + 1] == close_parentheses[c]:
                len_s -= 2
                if 0 < len_s:
                    s = s[0:i] + s[i + 2:]
                    i -= 1                    
                continue                
            i += 1

        return len_s == 0 
```

Enter fullscreen mode Exit fullscreen mode