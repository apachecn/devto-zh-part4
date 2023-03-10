# LeetCode:“回文数”w/有趣的 JavaScript 数学方法✨

> 原文：<https://dev.to/acupoftee/leetcode-palindrome-number-w-fun-javascript-mathematical-approach-1obi>

这是我解释解决编码问题的方法的系列文章的一部分。这是为了帮助我更好地表达我的思维过程，并为开发人员启发新的解决问题的方法！

**问题陈述:**

> 判断一个整数是否是回文。当一个整数向后读和向前读一样时，它就是一个回文。跟进:你能在不把整数转换成字符串的情况下解决它吗？

**方法:**

现在肯定有一个超级有趣的 JavaScript one liner 迭代解决方案，但我也对后续方法感兴趣。

这种方法在数学上反转数字- *而不将其转换为字符串*，然后比较结果。

我决定写这种方法，因为数学解决方案比迭代或递归解决方案更有效。这是一个很好的数学思考编程的练习，因为 *[数学是一个奇妙的东西](https://www.youtube.com/watch?v=aa8U0nL-KXg)* 。

**解:**

```
/**
 * @param {number} x the number to check
 * @return {boolean} true if it's a palindrome number
 */
const isPalindrome = x => {
    if (x < 0) return false

    let reversed = 0, y = x

    while (y > 0) {
        const lastDigit = y % 10
        reversed = (reversed * 10) + lastDigit
        y = (y / 10) | 0
    }
    return x === reversed
} 
```

Enter fullscreen mode Exit fullscreen mode

**解释:**
首先，我们检查一下数字是否为负数。如果是，那么我们知道这不是一个回文，因为数字前后读起来是不同的。

```
if (x < 0) return false 
```

Enter fullscreen mode Exit fullscreen mode

如果数字是正数，我们将创建两个变量。第一个变量`reversed`将存储我们反转的数字，第二个变量`y`是我们输入数字的副本。`y`将用于撤销输入的数字，而不修改我们的原始输入。

以下步骤发生在 while 循环中:

使用模(`%`)运算符获取数字的最后一位。这是一个可以帮助你为将来的问题隔离最后一个数字的技巧。这里，我们将`y`除以 10 并返回余数。让我们参考输入`121`的例子。百位列`100`除以 10 余数为 0，十位列`20`除以 10 余数为 0。当我们将 1 列`1`除以 10 时，我们将得到 1 的余数，因为 1 不能被 10 整除。之后，我们将余数保存到`lastDigit` :

```
const lastDigit = y % 10 
```

Enter fullscreen mode Exit fullscreen mode

我们将最后一个数字添加到`reversed`。我们必须在赋值的右边将`reversed`乘以 10，以确保我们总是将`lastDigit`追加到“1”列。

```
reversed = (reversed * 10) + lastDigit 
```

Enter fullscreen mode Exit fullscreen mode

将`y`的最后一位数字除以 10，并截断最后一位小数。我们可以使用按位 OR 运算符`|`来实现这一点。**这是另一个在以后的 JS 问题中可以帮到你的窍门。**在本例中，我们将结果转换成一个整数，然后返回新的整数:

```
y = (y / 10) | 0 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果`reversed === x`，那么就是一个回文！

* * *

这个解决方案让我们不必遍历字符串数组，这意味着**我们不必为这个问题使用额外的存储空间**！当试图找到一个数学方法来解决一个编码问题时，想想你注意到的任何模式，问问自己是否需要一次读一个数字。如果是这样，你肯定可以用模运算和除法遍历一个数的数字。

* * *

*感谢阅读！一如既往，我们鼓励提问、反馈和想法。黑客快乐！*