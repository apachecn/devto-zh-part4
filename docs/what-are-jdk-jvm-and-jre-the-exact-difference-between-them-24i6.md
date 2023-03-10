# 什么是 JDK、JVM 和 JRE？它们之间的确切区别。

> 原文：<https://dev.to/jay_tillu/what-are-jdk-jvm-and-jre-the-exact-difference-between-them-24i6>

我们在学习的时候都会问问题。优秀的程序员从不直接接受事物，他们总是提出问题，并深刻理解系统到底是如何工作的。如果你知道系统的内部工作，并且理解代码的每一部分，它总是给你更多的信心。

所以现在当我开始学习 java 的时候，我下载的第一个东西就是 JDK。在那一瞬间，我的意识问我，“老弟，你知道什么是 JDK 吗？”这是旅程开始的第二天。让我们看看什么是 JDK、JRE 和 JVM，以及它们是如何工作的？

[![](img/857aa7a746b81604b19704565afc828b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_ixj2WFX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/700/1%2AkWwENu8pRVnGHk9ziJ6y7A.png)

## JVM (Java 虚拟机)

* * *

它只是有助于在您的设备上执行程序。JVM 只是提供了一个运行程序的环境。

下面是 JVM 的四个主要任务:

1.  加载代码。
2.  验证代码。
3.  执行代码。
4.  提供运行时环境。

没有 JVM，你不能运行基于 JVM 的程序。

JVM 是平台相关的。这意味着我们对每个软件和硬件平台都有不同的 JVM 配置。这意味着在 macOS 上运行的 JVM 不能在 windows 上运行，对于 windows，你必须下载 JVM 的 windows 版本。

## JRE (Java 运行时环境)

* * *

每当我们试图运行代码时，JVM 需要一些库集和文件来执行代码，这些文件在 JRE 中提供。

***JRE = JVM +套库。*T3】**

所以要运行基于 JVM 的程序，JRE 是最低要求。

JRE 也是平台相关的。这意味着我们在不同的平台上有不同的 JRE 版本。

## JDK (Java 开发工具包)

这是一个功能齐全的开发套件。JDK 拥有创建强大 java 程序所需的所有开发工具。

***JDK = JRE +开发工具。*T3】**

JDK 包含以下工具:

1.  Java 运行时环境(JRE)。
2.  翻译。
3.  编译器(javac)。
4.  归档程序(jar)。
5.  文档生成器(javadoc)。
6.  其他一些必要的工具。

简而言之，如果你只想运行基于 java 或 JVM 的代码，那么 JRE 是必需的。但是如果你想开发基于 java 或 JVM 的程序，那么 JDK 是必需的。

所以，伙计们，这都是关于 JVM，JRE 和 JDK。如果我错过了什么，请在评论中告诉我。

请每天查看关于 Kotlin 和其他编程主题的新帖子。在那之前，继续编码，继续爱。

> 想和我联系吗？以下是链接。我很乐意成为你的朋友。😊
> [Twitter](https://twitter.com/jay_tillu)
> [脸书](https://www.facebook.com/jaytillu.1314/)
> [insta gram](https://www.instagram.com/jay.tillu/)
> [Medium](https://medium.com/jay-tillu)
> 或者直接发邮件给我[jayviveki13@gmail.com](mailto:jayviveki13@gmail.com)