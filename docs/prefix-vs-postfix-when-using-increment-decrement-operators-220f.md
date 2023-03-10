# 使用递增和递减运算符时前缀和后缀。

> 原文：<https://dev.to/jamesthomson/prefix-vs-postfix-when-using-increment-decrement-operators-220f>

*Cover photo: [@markusspiske](https://unsplash.com/photos/4W5WWKaxsKs)*

无论您使用 JavaScript 的时间是 10 天还是 10 年，您肯定都遇到并使用过增量(`++`)和减量(`--`)操作符。

但是你是否意识到，当你在操作数上使用前缀和后缀时，它们的用法是不同的？

## 区别

首先，让我们看看当我们使用后缀增量运算符时会发生什么。

根据这种说法，您希望控制台记录什么？

```
let count = 0
console.log(count++)
console.log(count) 
```

Enter fullscreen mode Exit fullscreen mode

您可能期望它为两者记录 1，但事实并非如此。它将记录 0，然后记录 1。

```
let count = 0
console.log(count++) // 0
console.log(count) // 1 
```

Enter fullscreen mode Exit fullscreen mode

为什么？因为后缀运算符会在应用运算符之前返回操作数**的值。**

现在，让我们尝试完全相同的代码，但是带有前缀运算符:

```
let count = 0
console.log(++count) // 1
console.log(count) // 1 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，现在首先应用操作符，然后在之后返回值**。**

## 结论

那么，这里有什么启示呢？我认为仅仅是意识到 JavaScript 的复杂性。使用这些操作符没有害处，但是您应该知道它会如何影响您的代码。有时候，走明确的路线甚至会更好。

不管怎样，我希望你学到了一些东西！

一如既往，快乐编码🤓