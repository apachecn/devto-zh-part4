# 为卑微的控制台辩护

> 原文：<https://dev.to/joeattardi/in-defense-of-the-humble-console-log-4k40>

对于使用`console.log`进行调试有一些负面情绪。的确，在很多情况下，浏览器的调试器更强大。然而，仍然存在使用`console.log`非常有用的场景。

最近我在调试一个拖放问题。我的一些拖放区不接受拖放。在调试器中使用断点在这里实际上不起作用。我想在拖动东西时看到调试信息。想象一下，在每个`dragenter`或`dragover`事件上遇到一个断点。不实用。每当我移动鼠标时，断点就会被触发。我必须点击恢复按钮，在这种情况下，我可能会失去我的主动拖动操作，或者单手按下恢复热键(记住，我是用一只手的手指按住鼠标左键
)。

相反，我添加了一些`console.log`语句来打印关于被拖动的元素的信息，它是否是一个有效的拖放区，等等。这产生了很多日志消息，但我仍然认为这比每次移动鼠标时暂停执行要容易得多。

在第三方代码中使用 logpoints 会更加方便。

## 日志点:动态添加`console.log` s，甚至在第三方代码中

登录点是 Chrome 和 Firefox 中相对较新的功能。它们的工作方式类似于断点——将它们添加到一行代码中。除了不是暂停执行，而是简单地记录一些信息。您可以将 logpoints 放在任何地方，而不仅仅是您自己的代码。如果您可以在源代码面板中看到代码，您可以添加一个日志点。

我还没有在 Firefox 中使用过 logpoints，但是在 Chrome 中添加它们很简单。打开要添加日志点的代码，右键单击行号。选择“添加日志点...”：

[![](img/9e5d2c89c0a6e8a75f3751115879263b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fng_5Wph--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vjpad3ys6tu3kucsmckh.png)

然后会出现一个框，您可以在其中键入要记录的表达式。这个表达式的格式就像传递给对`console.log`的调用一样。您可以记录多个值，用逗号分隔它们。

[![](img/9e5d2c89c0a6e8a75f3751115879263b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fng_5Wph--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vjpad3ys6tu3kucsmckh.png)

现在，每当点击该行代码时，就会打印出配置的日志消息。

## 最后一个音符

虽然语句在开发和调试中非常有用，但是您应该在投入生产之前将它们去掉。

过多的日志消息会让好奇的用户洞察应用程序的内部工作方式。错误应该在
应用程序的 UI 中显示给用户，而不是记录到控制台。

一个额外的考虑:如果有许多日志记录语句正在执行，这可能会影响应用程序的性能。

## 进一步阅读

*   [像专业人员一样使用控制台调试 Javascript 的 10 个技巧](https://medium.com/appsflyer/10-tips-for-javascript-debugging-like-a-pro-with-console-7140027eb5f6)
*   [铬记录点](https://developers.google.com/web/updates/2019/01/devtools#logpoints)
*   [火狐登录点](https://developer.mozilla.org/en-US/docs/Tools/Debugger/Set_a_logpoint)