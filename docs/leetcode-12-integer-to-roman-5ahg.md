# leet code”12。整数到罗马"

> 原文：<https://dev.to/takakd/leetcode-12-integer-to-roman-5ahg>

[12。](https://leetcode.com/problems/integer-to-roman/)整数到罗马

```
class Solution:
    def intToRoman(self, num: int) -> str:

        if num == 0:
            return ''

        symbols = [('I', 'V'), ('X', 'L'), ('C', 'D'), ('M')]
        roman = ''        
        numOfDigit = 0

        while num > 0:
            c = ''
            digit = num % 10

            if digit == 4:
                c = symbols[numOfDigit][0] + symbols[numOfDigit][1]
            elif digit == 9:
                c = symbols[numOfDigit][0] + symbols[numOfDigit + 1][0]
            else:
                if digit > 4:
                    c = symbols[numOfDigit][1]
                for i in range(digit % 5):
                    c += symbols[numOfDigit][0]

            # next digit
            num //= 10
            numOfDigit += 1

            roman = c + roman            

        return roman 
```

Enter fullscreen mode Exit fullscreen mode