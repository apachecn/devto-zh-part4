# 编码概念-提升

> 原文：<https://dev.to/chris_bertrand/coding-concepts-what-is-hoisting-5emg>

提升是将声明移动到当前范围顶部的行为。这是 JavaScript 中使用 **var** 关键字时的默认行为，但是使用新的 **let** 和 **const** 关键字意味着这不会发生。

[![Image result for window cleANER crane BANNER](img/8297ee5e127faa3ed10de47eb5c2124a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oTBYk7D6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ccscleaning.com/wp-content/uploads/2018/12/industrial-cleaning-services-banner.jpg)

## 吊装在行动。

[https://codepen.io/chrisbertrand/embed/yddNze?height=600&default-tab=js,result&embed-version=2](https://codepen.io/chrisbertrand/embed/yddNze?height=600&default-tab=js,result&embed-version=2)

这里我们可以看到变量在使用后被声明，那么这是如何工作的呢？

## 怎么做，为什么？

【JavaScript 为什么要这么做？ *提升*是在解释器第一次运行代码时完成的。JavaScript 引擎被设计成这样，你的代码运行两次，第一次是通过一些语法检查和任何使用全局对象方法的东西。然后它会做一些优化来提高性能，这就是提升的用武之地。第二遍是代码运行的地方。

> 提升会将所有声明移动到当前作用域的顶部(当前文件或函数的顶部)。

## 初始化不会被提升。

在这个例子中，你可以看到当在同一行声明和初始化一个变量时，javascript 引擎并没有优化这个代码块。因此，当试图访问变量时，您会得到一个未定义的错误。

[https://codepen.io/chrisbertrand/embed/yddeyx/?height=600&default-tab=js,result&embed-version=2](https://codepen.io/chrisbertrand/embed/yddeyx/?height=600&default-tab=js,result&embed-version=2)

## Let 和 Const 的行为不同

[https://codepen.io/chrisbertrand/embed/qzzNjL?height=600&default-tab=js,result&embed-version=2](https://codepen.io/chrisbertrand/embed/qzzNjL?height=600&default-tab=js,result&embed-version=2)

当将第一个例子改为使用 **let** 或 **const** 关键字而不是 **var** 时，一切都完了。如果你打开你的控制台窗口，你会发现 JavaScript 引擎抛出异常(如果你不介意，我已经添加了图片😉)

您可以看到结果窗格中没有任何内容，这是因为这些新关键字的行为不同。下面的错误很容易理解，一个**常量**在声明时需要初始化，而一个 **let** 在解释器的第一个周期中被检查。

[![](img/286df79d136a5d342f28b7d6e2763371.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6AcvOt3M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.designpuddle.com/wp-content/uploads/2019/07/image-1.png)

[![](img/f6d40d5d7d6dce481e6f9be77033b047.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bsu7rRYs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.designpuddle.com/wp-content/uploads/2019/07/image.png)

## 将您的声明放在范围内。

吊装很酷吧？JavaScript 如此宽容，允许这样的事情发生，这很好。同样，这也意味着代码在你不期望的时候工作。今后，最好将所有声明放在每个作用域的开头！随着 **let** 和 **const** 关键字的引入和大规模采用，了解它们的区别是很重要的，要知道简单地将所有的**变量**改为**let**会在应用程序中引起问题。

## 功能呢

提升也适用于*函数声明*，这就是为什么你可以在调用它们之后定义你的函数！然而，**函数表达式/匿名方法**不会像 let 和 const 那样被提升。看一下这最后一个代码，我们可以看到变量声明是从底部开始的，但是匿名函数调用没有，这就消除了引擎抛出的错误。

[https://codepen.io/chrisbertrand/embed/MMMedB?height=600&default-tab=js&embed-version=2](https://codepen.io/chrisbertrand/embed/MMMedB?height=600&default-tab=js&embed-version=2)

[![](img/bdc5ad59c4169fcb69647188983ea182.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AZVnm4Y9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.designpuddle.com/wp-content/uploads/2019/07/image-2.png)

最后，最好知道**函数在变量**之前被提升！因此，当重构遗留代码时，记得查看所有变量的范围，并考虑解释器将如何解析您正在修改的代码。

> 这主要是因为 let 和 const 是块范围的。另一方面，风险值不是，因此可以上升到全球范围(只要“严格使用”不存在
> 
> 詹姆斯-[https://dev.to/jamesthomson](https://dev.to/jamesthomson)

### 资源

*   [https://www.w3schools.com/js/js_hoisting.asp](https://www.w3schools.com/js/js_hoisting.asp)
*   [https://en.wikipedia.org/wiki/JavaScript_engine](https://en.wikipedia.org/wiki/JavaScript_engine)
*   [https://www . codingame . com/playgrounds/7974/understanding-raising-in-JavaScript](https://www.codingame.com/playgrounds/7974/understanding-hoisting-in-javascript)
*   [https://code burst . io/JavaScript-what-is-raising-DFA 84512 DD 28](https://codeburst.io/javascript-what-is-hoisting-dfa84512dd28)