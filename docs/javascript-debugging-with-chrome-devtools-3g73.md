# 使用 Chrome DevTools 调试 JavaScript

> 原文：<https://dev.to/daniel_werner/javascript-debugging-with-chrome-devtools-3g73>

## 简介

跟踪 JavaScript 代码中发生的事情并不总是微不足道的，尤其是当涉及许多事件处理程序和/或异步 ajax 调用时。在这些情况下使用 console.log 可能会令人厌倦且效率低下，在本文中，我将展示如何使用 Chrome DevTools 调试 JavaScript 的基础知识。

## 入门

打开检查器(Cmd+Option+i 或 Ctrl+Shift+i)，导航到 Sources 选项卡，并在左侧的导航栏上选择您想要调试的 JavaScript 文件。

[![](img/38dd7698726986135df1c8db04c5f684.png)](https://42coders.com/wp-content/uploads/2019/07/dev_tools.png)

通过单击行号添加断点:

[![](img/2c036e8ef19a3099a851e6ffc61bc812.png)](https://42coders.com/wp-content/uploads/2019/07/breakpoint.png)

在浏览器中运行代码，当调试器停止时，您可以使用通常的东西来调试您的代码，包括:

*   进入功能–F11
*   走过来——F10
*   跳出函数调用–Shift+F11
*   观察变量
*   检查局部变量

## [![](img/462638295ee1b2c61deb4779c38d4e33.png)T4】](https://42coders.com/wp-content/uploads/2019/07/debugging.png)

## 附加有用功能

我最近学到了一些在调试时可以使用的非常方便的特性。

#### 调试器

添加调试器；命令添加到代码中，调试器将停止执行代码，这是添加断点的一种非常快捷的方式，无需在 devtools 中浏览源文件。

#### 控制台.表()

类似于 console.log，但它以表格形式显示变量，这对于快速检查复杂变量的值很有用。

#### 漂亮的印花

如果您需要检查/调试缩小的代码(尤其是在生产中)，devtools 可以通过单击主调试器窗口左下角的花括号来打印缩小的代码:

[![](img/3fda7414272126f7bbb0f573be0c0982.png)](https://42coders.com/wp-content/uploads/2019/07/minified.png)

漂亮的打印输出不如原始源代码清晰有用，但比缩小版更容易理解/调试，如下所示:

[![](img/65787220f237d4259ddd59d43084265f.png)](https://42coders.com/wp-content/uploads/2019/07/pretty_print.png)

devtools 还有更多的功能，如果你有兴趣，请查看这里的官方文档:[https://developers . Google . com/web/tools/chrome-dev tools/JavaScript/](https://developers.google.com/web/tools/chrome-devtools/javascript/)

用 Chrome DevTools 调试 JavaScript 的帖子[最早出现在](https://42coders.com/javascript-debugging-with-chrome-devtools/) [42 Coders](https://42coders.com) 上。