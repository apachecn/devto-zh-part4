# JavaScript 解析器和引擎

> 原文：<https://dev.to/idevkamboj/javascript-parser-and-engine-3c1c>

在这篇文章中，我会很快告诉你。我们编写的 JavaScript 代码实际上是如何执行的。

[最初发布于](https://devkamboj.in/javascriptEnginesAndParsing/)

所以，JavaScript 总是托管在某个环境中。这是最典型的浏览器，如谷歌浏览器、火狐浏览器、Safari 浏览器等。这是 JavaScript 运行的地方。还可以有其他主机，比如 NodeJS web 服务器，甚至是一些接受 JavaScript 代码作为输入的应用程序。但是，在本文中，我将只关注浏览器。

因此，当我们编写 JavaScript 代码并实际运行它时，有许多事情在幕后发生。因此，JavaScript 所在的主机有某种 JavaScript 引擎，它获取并执行我们的代码。简单来说，JavaScript 引擎是一个执行 JavaScript 代码的程序。有许多不同的引擎，比如

1.  谷歌的 V8 引擎，用于谷歌浏览器。
2.  蜘蛛猴是 Mozilla 的 JavaScript 引擎
3.  [JavaScriptCore](https://en.wikipedia.org/wiki/JavaScript_engine?source=post_page) 和[还有更多](https://en.wikipedia.org/wiki/List_of_ECMAScript_engines?source=post_page---------------------------)。

现在，在引擎内部发生的第一件事是，我们的代码被解析器解析，解析器基本上逐行读取我们的代码，并检查我们给它的代码的语法是否正确。这意味着解析器知道 JavaScript 规则，知道如何编写它才能正确有效。如果你犯了一些错误，它会抛出一个错误并停止执行。

[![javascriptParser](img/0e0a4e6c48da49ab5209deaa35afd627.png "javascriptParser")](///static/9f96480e9c1dc593ce716091bccdab10/e334e/javascriptParser.png)

如果一切都是正确的，那么解析器产生一个称为抽象语法树的数据结构，然后被翻译成机器代码。所以这段代码不再是 JavaScript 代码，而是一段代码，或者说是一组指令，可以由计算机的处理器直接执行。只有当我们的代码已经转换成机器码，才能真正运行并完成它的工作。

[![javascriptEngine1](img/3ff1986b0e33edcc16390d70a0aadf67.png "javascriptEngine1")](///static/9d5ffc27e5c0a859e9cf4b183fc897c2/e334e/javascriptEngine1.png)

我只是想让你们知道，一旦我们选择运行我们的代码，实际上会发生什么。实际上幕后还有更多的事情在进行。不同的引擎做事情的方式略有不同。