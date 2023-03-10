# 让我们一边做东西，一边学围棋

> 原文：<https://dev.to/projectpage/let-s-build-something-and-learn-go-at-the-same-time-31hh>

大家好，我是肯尼，我以写围棋代码为生，业余爱好是运营一家面向围棋程序员的新闻网站[golangnews.com](https://golangnews.com)。我使用 Go 已经有一段时间了，我想我应该写一些教程来向其他人展示它有多棒。

跟着学围棋，在做的时候做一些很棒的东西。

本课程是对 Google 的 Go 编程语言的介绍。我尽量不假设太多的知识——即使你以前没有做过太多的编程，只要一点点努力就应该是平易近人的，所以如果你对任何问题不确定，或者认为事情没有解释清楚，请在下面的评论中提问。在接下来的几个月里，我会每隔几天试着发一篇帖子，到最后你应该会精通围棋，并且拥有一台自己的服务器，所以如果你想一口一口地学习这门语言，就跟着我吧。

你为什么想学围棋？Go 是编写互联网服务器的理想选择——它为许多我们认为理所当然的互联网服务提供动力；Youtube、Monzo、Ironio、SendGrid、GitLab 和 Digital Ocean 等公司都在使用它。

为了让我们保持动力，我们将建立一些真实的东西——让我们在学习围棋的同时建立一个更好的 twitter(暂时忽略规模问题，我们将保持简单——一个程序，一个服务器)。我准备把它放在这里:[53words.com](http://53words.com)。我们将在几节课中让该服务器启动并运行。

目前这个领域还没有任何东西，在课程结束时，我们会有一个更好的 twitter(更少的广告、纳粹和潮湿的模因)，它可能还会以某种方式涉及数字 53。所以我们走吧！

首先，我们需要学习围棋。它只有 25 个关键词，这是一个很好的起点。这是我们的前三个关键词:

*   *包*——这是 go 中代码的基本[单元，你可以把它想象成文件夹的同义词——你电脑上的一个文件夹就是 go 中的一个包](https://golang.org/ref/spec#Packages)
*   *import*——这允许你从其他地方导入代码包/文件夹，包括由 Go 作者提供的代码的[标准库](https://golang.org/pkg/#stdlib)。
*   *func* -这代表[函数](https://en.wikipedia.org/wiki/Function_(mathematics))，一组用于转换数据的指令

因此，包/文件夹包含文件，这些文件包含转换数据的函数。

你还不需要安装任何东西，要开始，我们将使用[play.golang.org](https://play.golang.org/p/jKrWXZvT3B4)-这是一个交互式围棋网站，让你从浏览器执行围棋程序。如果你打开那个链接，你会发现这个代码:

```
// Package main is the entry point of your program
// the first and only function in your program called by the operating system
package main

// import lets you import lots of packages
import (
    // fmt is a package for reading and writing stuff (mostly text)
    // you can use it to print things out (as below)
    "fmt"
)

// func then a name defines a function
// main is the first and only function called from outside
// after that, you're on your own
func main() {

    // fmt.Println writes the things you give it to standard output
    fmt.Println("Hello", "World")

} 
```

上面的引号定义了字符串(人类的单词)，我们将在下一个教程中讨论类型和 go 中不同的内置类型。让我们试着在这里做一个改变——用引号将你的名字加上，并打印出来，而不是“World ”,然后按顶部的 Run，看看下面的结果。

回来了吗？太好了。如果你打印了你的名字，让我们试试别的，在 Println 函数的末尾添加数字 53 和文本“words ”,看看你会得到什么，比如:

```
fmt.Println("Hello", "World", "Kenny", 53, "Words") 
```

你会发现 [Println](https://golang.org/pkg/fmt/#Println) 打印出你扔给它的大多数东西(偶数或其他类型的东西)，因为它有一些技巧来以正确的方式格式化每一件东西。

有什么问题吗？请在下面的评论中告诉我。您可以在[play.golang.org](https://play.golang.org/p/8NrBnO0s1M5)运行本课的最终代码，而无需离开浏览器。