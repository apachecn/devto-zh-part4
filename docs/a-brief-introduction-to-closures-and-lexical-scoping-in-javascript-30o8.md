# JavaScript 中闭包和词法范围的简要介绍

> 原文：<https://dev.to/lelouchb/a-brief-introduction-to-closures-and-lexical-scoping-in-javascript-30o8>

#### “用 ECMAScript 语言写东西不理解闭包就像写 Java 不理解类一样”JSON 之父道格拉斯·克洛克福特

<figure>[![](img/f4d1aa67d91923e5ce14a2b469516c85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D34WJ-_V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AckWxE4b3zxi1bgZoJFFxOw.jpeg) 

<figcaption>照片由[卡斯帕·卡米尔·鲁宾](https://unsplash.com/@casparrubin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上传 [Unsplash](https://unsplash.com/search/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

在这篇文章中，我们将讨论 JavaScript 中的闭包和词法范围。理解闭包有助于更好地理解编程本身。如果你打算成为一名职业程序员，关于闭包及其应用的问题会在技术面试中被广泛问到，并且会对你很有帮助。

如果您是一名 JavaScript 开发人员，很可能您已经在使用闭包，只是不知道而已。有太多复杂的例子让你在学习闭包时感到困惑，所以我将使用我能找到的最简单的例子，到本文结束时，你应该对什么是闭包有了基本的理解。让我们开始吧。

### 词法作用域

在讨论闭包之前，我们应该对作用域和词法作用域有一个基本的了解。JavaScript 具有带函数作用域的词法作用域，这意味着每个函数创建一个新的作用域。

> 什么是函数作用域？

基本上，在 JavaScript 中，有全局范围和局部范围。

> **全局作用域**:一个 Javascript 文档中只有一个全局作用域，即所有函数之外的区域，你如何识别全局作用域就是定义在全局作用域中的变量可以在代码中的任何地方被访问。
> 
> **局部作用域**:函数内部声明的变量是函数的局部变量，并且绑定到相应的局部作用域。这些变量不能在函数之外访问。

让我们看一个例子: