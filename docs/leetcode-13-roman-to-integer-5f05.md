# leet code”13。罗马到整数"

> 原文：<https://dev.to/takakd/leetcode-13-roman-to-integer-5f05>

嗯...
如何去掉一大堆`pos += 1`和`pos < len_str`

13。罗马到整数

```
class Solution:
    def romanToInt(self, s: str) -> int:

        if not s:
            return 0

        pos = 0
        len_str = len(s)
        numOfDigits = 0
        symbols = ['dummy', ('I', 'V', 'X'), ('X', 'L', 'C'), ('C', 'D', 'M'), ('M')]
        s = s[::-1]
        value = 0

        while pos < len_str:
            num = 0
            numOfDigits += 1

            # ex. I ... III, VI ... VIII
            if s[pos] == symbols[numOfDigits][0]:      
                num = 1
                pos += 1
                while pos < len_str and s[pos] == symbols[numOfDigits][0]:
                    num += 1
                    pos += 1
                if pos < len_str and s[pos] == symbols[numOfDigits][1]:
                    num += 5
                    pos += 1

            # ex. V, IV
            elif s[pos] == symbols[numOfDigits][1]:
                num = 5
                pos += 1
                if pos < len_str and s[pos] == symbols[numOfDigits][0]:
                    num -= 1
                    pos += 1

            # ex. IX, XC, M
            elif s[pos] == symbols[numOfDigits][2]:
                num = 10
                pos += 1
                if pos < len_str and s[pos] == symbols[numOfDigits][0]:
                    num -= 1
                    pos += 1

            value += num * (10 ** (numOfDigits - 1))

        return value 
```

Enter fullscreen mode Exit fullscreen mode