# LeetCode "Pow(x，n)"

> 原文：<https://dev.to/takakd/leetcode-pow-x-n-5g7n>

[Pow(x，n)](https://leetcode.com/problems/powx-n/)

我惊讶于每个人都能写简短的代码😨

```
class Solution:
    def myPow(self, x: float, n: int) -> float:

        if n == 0:
            return 1
        elif n == 1:
            return x
        elif n == -1:
            return 1/x

        n1 = int(n / 2)
        v = self.myPow(x, n1)
        if n % 2 == 0:
            v *= v
        else:
            v *= self.myPow(x, n - n1)                

        return v 
```

Enter fullscreen mode Exit fullscreen mode