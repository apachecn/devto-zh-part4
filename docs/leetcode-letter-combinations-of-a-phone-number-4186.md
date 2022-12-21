# LeetCode“电话号码的字母组合”

> 原文：<https://dev.to/takakd/leetcode-letter-combinations-of-a-phone-number-4186>

我认为分离组合的过程是一个很好的方法。

[电话号码的字母组合](https://leetcode.com/problems/letter-combinations-of-a-phone-number/)

```
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:

        if not digits:
            return []

        DecimalToChar = {'2': ['a', 'b', 'c'],
                         '3':['d', 'e', 'f'],
                         '4':['g', 'h', 'i'],
                         '5':['j', 'k', 'l'],
                         '6':['m', 'n', 'o'],
                         '7':['p', 'q', 'r', 's'],
                         '8':['t', 'u', 'v'], 
                         '9':['w', 'x', 'y', 'z'],
                         '0':[' ']
                        }

        def combinations(strs1, strs2):
            combs = []
            for s1 in strs1:
                for s2 in strs2:
                    combs.append(s1 + s2)
            return combs

        strs = [DecimalToChar[s] for s in digits]        
        combs = strs[0]
        for s in strs[1:]:
            combs = combinations(combs, s)

        return combs 
```

Enter fullscreen mode Exit fullscreen mode