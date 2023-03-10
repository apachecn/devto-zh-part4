# 当你不知道从哪里开始时，如何写 JavaScript

> 原文：<https://dev.to/canderson93/how-to-write-javascript-when-you-don-t-know-where-to-begin-5f3f>

你有没有试过一个编码问题，结果却盯着一个空白的编辑器？也许你试图写一个简单的函数，但甚至不知道从哪里开始？

真是*令人沮丧*。

毕竟，你已经花了*个月*来理解 JavaScript 的基础知识。你已经学习了所有关于变量、数组和函数的知识...但是当你自己编码时，你很难写出一行代码。

你不是不知道 JavaScript——你完全有能力*读*代码。只是到了落笔的时候，少了点什么。

你如何通过空白编辑器？

首先，重要的是你不要气馁。开始另一门课程不是解决问题的方法。你*所做的*需要的是一种分解你的想法的方法，这样你就知道该写什么代码。

我已经分解了我用来解决编码问题的系统，并提供了如何从想法到编码的一步一步的指导。我将通过一个使用斐波那契数列的例子来展示它是如何工作的。

## 如何分解问题

(注意:我在这里经常提到“英语”，但你应该使用你最熟悉的语言)

初学者试图接近编码的一个缺陷是他们试图用 JavaScript 解决问题。这似乎是一个愚蠢的说法，所以我换一种方式说:*你需要先用英语解决这个问题。*

[![How to Write JavaScript When You Don’t Know Where to Begin](img/30b6b2e106214379fbb3b1152b65bcf2.png)](https://i.giphy.com/media/JWF7fOo3XyLgA/giphy.gif)

当我们教 JavaScript 时，我们向你展示语言的组成部分。我们没有做的是解释如何用 JavaScript 表达想法。

你需要先用英语表达你的想法，然后*把*翻译成 JavaScript。这是通过以下四个步骤完成的:

1.  用英语定义你的问题
2.  用英语定义一个*解决方案*
3.  用**伪代码**写下你的解决方案
4.  将您的解决方案翻译成 JavaScript

第一次做的时候，这可能并不容易——需要一些时间来适应。口语不会强迫你在编码所要求的水平上阐明你的想法。

随着你做得越来越多，你会理解你能用代码做什么，不能做什么，并且获得一种天生的感觉，知道如何用一种容易翻译的方式来表达你的想法。

### 用伪代码编写

伪代码是代码和英语的融合。编写伪代码没有正确的方法——你可以边做边编。只要它看起来有点像代码，你就做得很好。

我们编写伪代码的原因是它允许我们以一种语言不可知的方式编写。我们用它来跳过不必要的细节，这样我们可以很容易地将*转换成*代码。

与简单地编写 JavaScript 相比，伪代码的美妙之处在于我们可以选择我们喜欢的任何级别的细节。我们可以使用“神奇的”语句来勾画我们的想法，而不必担心实现细节。如果有帮助的话，我们甚至可以用完整的句子写伪代码。

```
//Both of these functions are perfectly valid pseudo-code
printSortedArray(array):
    sort array
    print each item in array

printSortedArray(array):
    // Bubble sort
    while array is not sorted:
        for each item in array:
            if item+1 is less than item
                swap item and item+1

    for each item in array:
        print item 
```

Enter fullscreen mode Exit fullscreen mode

## 举例:斐波那契数列

我将快速浏览一个分解问题并编写伪代码的例子。我们将创建一个函数，返回斐波那契数列的第 n 个值。

在整个过程中有两点需要注意:

*   这是非常私人的。我想出来的可能和你不一样。
*   它比看起来要长。这是一篇博客文章，你可以直接跳到最终结果。你看不到花在思考问题上的时间。

### 简明英语

我们这一步的主要目标是*澄清一切*。我们越具体越好。最初，简单地*定义*一切是个好主意，然后开始陈述关于你的定义的事实。

如果我们定义斐波纳契数列问题:

*   斐波那契数列是序列`1, 1, 2, 3, 5, 8`等。
*   要计算斐波纳契数列的任何值，我必须知道数列中前两个值。
*   如果我想知道序列中的一个特定值，我需要知道链中每一个先前的值。

这可能看起来不多，但它确实给了我们足够的时间来定义一个简单的解决方案:

*   为了得到`n`的值，我需要生成直到`n`的整个斐波那契数列。

如果你有能力(并且问题允许)，你可以想出多个解决方案，然后选择*一个。*如果不成功，能够回来尝试一条不同的路也不错。

### 伪代码

好吧。我们现在有了一个特定的方法来获得斐波纳契数列的第`n`个值:创建直到 n 的所有数字。

正如我提到的，伪代码的美妙之处在于它让我在不同的细节层次上解决问题。第一次使用“魔法”来解决问题，并在我们需要时添加细节，通常是值得的。

这就是我如何设计一个“神奇”的解决方案:

```
fibonacci (n):
    loop between 0 and n
        sum two previous fibonacci numbers
        when n, return value 
```

Enter fullscreen mode Exit fullscreen mode

由于神奇的“前两个斐波那契数”，它不够详细，无法直接转化为代码，但基本方法是可靠的。我们完全可以以此为蓝本开始编码，边走边填补空白。

由于这是一个演示，我们将再做一轮伪代码来添加更多的细节。

在这一轮中，我们希望少一点魔力:*我们如何得到前面两个斐波那契数？*

```
function fibonacci(n):
    // We can't get n-2 for 0 and 1, so return them directly
    if n is 0 or 1, return 1

    // Set these to the first two numbers of the fibonacci sequence
    prev1 = 1
    prev2 = 1

    // use 2 here because we've already done when n is 0 and 1
    loop from 2 until n:
        current = prev1 + prev2

        // Update the previous numbers, so we're ready for the next loop
        prev2 = prev1
        prev1 = current

   return current 
```

Enter fullscreen mode Exit fullscreen mode

乍一看，这与我们之前的解决方案完全不同，但实际上完全相同。我们刚刚阐明了如何在变量中存储之前的斐波纳契值。

这是一个完整的解决方案。虽然我可能需要在编码时添加一些小细节，但它将几乎 1:1 地翻译成 JavaScript。

### JavaScript

这一步应该是不言自明的。把你手上的伪代码代码，变成最终的 JavaScript。

您可能需要做出一些最终的澄清决定(比如选择在 for 循环中使用`<=`),但这看起来应该与您的伪代码非常相似。

```
function fibonacci(n) {
    // We can't get n-2 for 0 and 1, so return them directly
    if (n === 0 || n === 1) { return 1; }

    let prev1 = 1;
    let prev2 = 1;
    let current;

    // use 2 here because we've already done when n is 0 and 1
    for (let i = 2; i <= n; i++) {
        current = prev1 + prev2;

        // Update the previous numbers, so we're ready for the next loop
        prev2 = prev1;
        prev1 = current;
    }

    return current;
} 
```

Enter fullscreen mode Exit fullscreen mode

这不是斐波纳契数列最简洁的解决方案，但却是非常有效的。它将*肯定*通过编码面试。

就像我之前说的，这在开始的时候可能对你来说并不自然。你下一步要做的是练习。你可以去像 [HackerRank](https://www.hackerrank.com/) 这样的网站尝试他们的一些编码问题，或者你可以开始着手你自己的[实践项目。](https://dev.to/canderson93/how-to-find-ideas-for-good-practice-projects-5hcm)

* * *

你还停留在 JavaScript 教程上吗？注册我的时事通讯，获取帮助你度过难关的文章，并继续做你想做的事情