# 我的一样

> 原文：<https://dev.to/jacoby/my-shame-3e50>

[![My Shame](img/374aaed20e450a44cf380fbf33bd078c.png)](https://twitter.com/JacobyDave/status/1151577935117279232/)

我发了这条微博，说“我的耻辱”。

起初有 **`format_all_that_data`** 。它是一个内部函数，采用大数据结构并将其转换成 HTML。

使用 Perl 的`CGI.pm`创建 HTML。十年前我就知道这是个坏主意，但这是实验室风格，过了一段时间我才明白模板工具包(和其他模板引擎)是未来的发展方向。

我需要做一些改变，但是我必须在生产中工作(这种情况我仍然没有逃脱)，并且希望保持旧的那个工作，所以我把它叫做 **`format_all_that_data_new`** ，当它完成时，我把我所知道的使用`format_all_that_data`的地方改为`format_all_that_data_new`，并且使`format_all_that_data`返回一个空字符串。这样，如果预期有产出但没有实现，就会有人告诉我。

然后我被要求做一些小改动。一个变化就可以了；做它，测试它，释放它，忘记它。但是还有更多的变化，这足以让我们深入到模板化和修复程序中的许多其他东西。同样，我是在生产中开发，所以我创建了 **`format_all_that_data_tt`** ，它使用[模板工具包](http://www.template-toolkit.org/)来正确处理 HTML*。*

 *而且，一旦有了，我想切换过来，但又想把改动减到最小，所以我把`format_all_that_data_new`移到了 **`format_all_that_data_old`** ，让它和我卡在里面，害怕移除的所有知识都在那里，却没有消失。

所以我把 **`format_all_that_data_new`** 全部改写为:

```
sub format_all_that_data_new($data) { format_all_that_data_tt($data) } 
```

我保留死代码，因为我害怕，并添加 cruft 来绕过它。

这是我的耻辱。

#### 如果有什么问题或意见，我很乐意倾听。在[推特](https://twitter.com/jacobydave)或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。*