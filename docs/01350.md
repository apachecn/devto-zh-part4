# 学习一门新语言

> 原文：<https://dev.to/austinbh/learning-a-new-language-1ml5>

作为熨斗学校 Web 开发项目的一名应届毕业生，我在过去的 4 个月里一直在使用 Ruby 和 JavaScript。我以前接触过其他语言，但是我没有尝试过自己学习一门新的语言和框架。

## 为什么是 Python？

[![](img/116fca9af83bd2f63599a720c840a5ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XsjpqOMQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.freeimages.cimg/large-previews/5eb/green-tree-python-1312700.jpg)

我对一门新语言持观望态度，决定(无论好坏)使用动态类型的语言，而不是像 Java 这样的静态类型的语言。我的主要兴趣是学习一种新的强类型语言，这是对 JavaScript 的一种改变，在过去的几个月里，我一直非常关注 JavaScript。

### 简述已键入的语言

为了理解静态和动态类型语言之间的区别，你需要理解的是静态语言更加严格，不允许类型变异。此外，在这种情况下，强类型是指在运行时防止类型错误。这基本上意味着，如果我在 Python 中使用了不正确的类型，我将收到一个错误，而不是像在 JavaScript 中那样返回 undefined。

## Python 初始响应

[![](img/577703c853370d8e2384d4bbc3bc9407.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pKb54tOm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.freeimages.cimg/large-previews/c1c/dog-1361477.jpg)

一旦我开始使用 Python，我立刻发现了一些我喜欢的东西。列表、字典、集合和生成器理解是我非常喜欢使用的东西。虽然在一般意义上，它基本上只是一种迭代这些结构的方法，但它们的语法非常吸引我。这里我们只举一个简单的例子。

```
new_list = [1, 2, 3, 4, 5]
new_list = [num for num in new_list if num % 2 == 0]
print(new_list)
#=> [2, 4] 
```

我在这里所做的只是迭代 new_list 并只返回偶数。这类似于 JavaScript 中的过滤函数，我们可以在数组上调用它。

```
newArray = [1, 2, 3, 4, 5]
newArray = newArray.filter(num => num%2 === 0)
console.log(newArray)
//=> [2, 4] 
```

然而，python 中的列表理解比过滤器更强大，它还可以执行与 JavaScript 中的 map 函数相同的操作。

```
new_list = [1, 2, 3, 4, 5]
new_list = [num*2 for num in new_list]
print(new_list
#=> [2, 4, 6, 8, 10] 
```

这只是 Python 中理解所能做的表面工作，但我非常喜欢它们的语法。

## 继续学习

对我来说，学习的最好方法之一就是简单地使用一些东西。在 Python 中，这意味着编写代码和制作应用程序。我尝试制作了几个不同的简单 CLI 应用程序来加深我对 Python 的理解。让我更容易掌握的一个相似之处是 Python 和 JavaScript 中的函数定义语法

```
# Python 
def foo():
    print('Hello World!')
foo() 
```

```
// JavaScript

function foo() {
    console.log('Hello World!')
}
foo() 
```

尽管这两个函数有点不同，但它们被调用的方式是相同的，这让我更容易掌握如何实际打印出“Hello World！”。

## 下一步

[![](img/653f326e30e82677ded5086fbc6c5da2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y0X0TgIL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.freeimages.cimg/large-previews/bab/sasha-1378238.jpg)

我计划能够继续使用 Django web 框架练习 Python。我已经开始使用 Django，但是需要更多的时间来实现它，就像我现在使用 Rails 应用程序一样。随着我继续努力学习 Python，我也可能会写更多关于 Python 的文章，所以请在这里关注更多与 Python 相关的帖子。

## 参考文献

*   [https://hacker noon . com/I-finally-understand-static-vs-dynamic-typing-and-you-will-too-ad 0 C2 BD 0 ACC 7](https://hackernoon.com/i-finally-understand-static-vs-dynamic-typing-and-you-will-too-ad0c2bd0acc7)
*   [https://en.wikipedia.org/wiki/Strong_and_weak_typing](https://en.wikipedia.org/wiki/Strong_and_weak_typing)
*   [https://en.wikipedia.org/wiki/Type_system](https://en.wikipedia.org/wiki/Type_system)