# 乱用 ClojureScript

> 原文：<https://dev.to/bachmeil/messing-with-clojurescript-5cff>

我对 Javascript 的使用仅限于从网上复制和粘贴一些代码片段。我不怎么做 web 开发，当我做的时候，我通常使用 CGI 和 D. JS 确实提供了一些很大的优势，例如不需要运行 web 服务器。

没学过 JS 的主要原因是不喜欢。这并不是由于语言中的缺陷——尽管像任何语言一样，这些缺陷是存在的——这纯粹是偏好的问题。

几年前我和 Clojure 合作过，但并没有真正很好地利用它，所以我把它放在一边，再也没有回来。ClojureScript 有一些吸引力，因为它允许我编写 Clojure 而不是 JS。所以昨晚我去查了一下。

我上了网站，发现了一个“快速启动”页面。我学到的第一件事是这不是真的:

> 如果您使用的是 macOS 或 Linux，那么本教程主要部分所需的唯一依赖项是 web 浏览器和 Clojure 的安装。

您实际上需要安装 Clojure *，如链接*所述。如果使用发行版的包管理器安装 Clojure，它就不起作用。

然后，我创建了几个目录和两个文件。我不得不猜测哪个目录是进行编译的目录，所以我尝试了一个包含源文件的目录(这看起来很自然，来自 C 世界)。没有。我必须转到顶层目录，从那里开始。

过了一会儿，一个浏览器标签打开了，我等待着什么事情发生。什么都没发生。出于某种原因，一个什么都不做的 html 文件被打开了。我回到了候机楼。我可以输入内容，但这与运行 Clojure repl 没有任何区别。在那一点上，我完成了我的“快速启动”。我觉得我没有开始做任何事情，因为我没有用 Clojure 替换 JS(这是我尝试 ClojureScript 的全部原因)。所以我决定四处寻找一个能告诉我该怎么做的向导。有很多链接，但是真的没有什么有用的。

我搜索了从 JS 调用 Clojure 函数的方法，它在 SO 上给我指出了[这个答案。从那里，我去了](https://stackoverflow.com/questions/31641030/call-clojurescript-from-javascript)[这个文档页面](https://clojurescript.org/reference/advanced-compilation#access-from-javascript)。我使用这些信息导出我的 Clojure 函数，编译一个 Clojure 库，并使用类似
的东西从 html 中调用这些函数

```
<button onclick="console.log(hello_world.core.average(3.4,7.8))">Click me</button> 
```

我认为问题的一部分是我不了解 JS，所以这些对我来说都不明显，显然 JS 的标准用例不是创建当你点击按钮时从 html 调用的函数。当你看到别人写 ClojureScript 有多好，但你甚至不知道如何做基本的事情时，这可能会令人沮丧。