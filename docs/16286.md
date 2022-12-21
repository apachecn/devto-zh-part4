# macOS 上的 Java 环境入门

> 原文：<https://dev.to/martingaston/getting-started-with-a-java-environment-on-macos-541c>

好吧，说实话:我完成了我的第一个 Java 项目，完全是一个专业开发人员，对 Java 构建/工具生态系统一无所知。我在 IntelliJ 中按了足够多的按钮，让程序运行起来，(谢天谢地)它们运行了，我在 IntelliJ 温柔的怀抱中度过了项目的剩余时间。

即兴表演吗？不，我称之为*时尚坠落*。

基本上，我发现理解 Java 工具非常令人困惑。所以在开始我的第二个 Java 项目之前，我想更好地理解到底发生了什么。

在接下来的几篇文章中，我们将学习更多关于 Java 是如何安装在我们的本地机器上的，然后构建一个简单的(只是有些做作的)`Hello World`应用程序，利用外部库和多个文件，这样我们就可以对标准的 Java 目录结构有所了解，以及如何从命令行构建我们的应用程序。然后，我们将在流行的构建工具 Gradle 的帮助下，继续整合相同的应用程序，最后，我们将项目整合到 IntelliJ IDE 中。在本系列的最后，我们应该对我们所有工具背后发生的事情有了更好的了解，并且对修补我们的项目构建有了更多的信心。

第一步:绝对基础。法医之旅的时间到了。第一次我很快就安装了 OpenJDK 12，我甚至不能告诉你我是怎么做到的。据我所知，下载 OpenJDK 会给你一个不喜欢的二进制文件目录，实际上对你应该如何处理它们没有任何指导。我感觉自己就像《三个男人和一个婴儿》中的~~史蒂夫·加顿伯格。~~

** * TL；dr:* *把未压缩的 OpenJDK 目录——比如`jdk-12.jdk`——复制到`/Library/Java/JavaVirtualMachines/`就好了。*

但是为什么呢？让我们解开这个谜团。

*警告*:这是目前 macOS 特有的。Linux 和 Windows 不可避免地会有所不同，并且很可能有自己的一系列小精灵。

### Java 实际安装在哪里？

系好安全带，因为从一个 Java 装置下来就像盯着克苏鲁的脸看了两个星期。下降到它的黑暗中心是一个保证，你会结束作为库尔茨。已经警告过你了。

让我们从顶部开始:

```
$ java --version
openjdk 12 2019-03-19
OpenJDK Runtime Environment (build 12+33)
OpenJDK 64-Bit Server VM (build 12+33, mixed mode, sharing) 
```

然后我们可以从`jshell`内部窥视我们的`java.home` [系统属性](https://docs.oracle.com/javase/tutorial/essential/environment/sysprop.html)。

```
jshell> System.getProperty("java.home")
$1 ==> "/Library/Java/JavaVirtualMachines/jdk-12.jdk/Contents/Home" 
```

好吧，酷。我们已经安装了 Java。这是最新的版本，幸运的是它不是 JDK 先知。事情看起来很乐观。那么我们的 shell 大概会指向我们的`java.home`目录吧？

```
$ which java
/usr/bin/java 
```

好的。但是这个文件有 74 个字节，如果你问我，这是一个信号，一个符号链接。这不是我们的第一次竞技！

```
$ greadlink -f $(which java)
/System/Library/Frameworks/JavaVM.framework/Versions/A/Commands/java 
```

嗯，那是新的。如果我们窥视一下`JavaVM.framework`目录，我们可以看到所有我们熟悉的 Java 文件。

```
$ ls -lh /System/Library/Frameworks/JavaVM.framework/Versions/Current/Commands/ | grep "java"
-rwxr-xr-x  1 root  wheel    57K 21 Mar 06:07 java
-rwxr-xr-x  1 root  wheel    74K 21 Mar 06:07 java_home
-rwxr-xr-x  1 root  wheel    57K 21 Mar 06:07 javac
-rwxr-xr-x  1 root  wheel    57K 21 Mar 06:07 javadoc
-rwxr-xr-x  1 root  wheel    57K 21 Mar 06:07 javah
-rwxr-xr-x  1 root  wheel    57K 21 Mar 06:07 javap
-rwxr-xr-x  1 root  wheel    57K 21 Mar 06:07 javapackager
-rwxr-xr-x  1 root  wheel    56K 21 Mar 06:07 javaws 
```

这些不是符号链接，所以很好。请注意，事情似乎还是不太对头。但是对[栈溢出的进一步挖掘表明这些是存根应用程序](https://stackoverflow.com/a/15133344),它们决定了要使用的 JVM 版本。你以前见过这样的东西吗？因为我没有。

更多的互联网搜索揭示了`java_home`，根据它的`man`页面:

> 返回适合设置 JAVA_HOME 环境变量的路径。它从 Java Preferences 应用程序中用户启用的和首选的 JVM 中确定这个路径。可以提供额外的约束来过滤可用的 JVM 列表。默认情况下，如果没有约束与可用的 JVM 列表匹配，则使用默认顺序。该路径被打印到标准输出。

只有我相当确定苹果很久以前就移除了 Java Preferences 应用程序，但是没关系。他们是不是只是从操作系统中移除了一半 Java 支持，把我们轻轻拴在机器中一个不被喜欢、不被维护的幽灵上？这是一个悬而未决的问题:我不知道。我只知道如果我们关心这样的一致性，我们就不会成为 Java 开发者。让我们启动它。

```
$ /usr/libexec/java_home
/Library/Java/JavaVirtualMachines/jdk-12.jdk/Contents/Home 
```

好吧！太棒了。我们终于到了`/Library/Java/JavaVirtualMachines`。我只是失去了部分理智，所以我会把这当成一场胜利。默认情况下，`java_home`似乎优先考虑 JDK 的最新版本，所以如果你已经安装了 12 个版本，当你运行`java`、`javac`、`jshell`等时，它将取代 11 个版本。您可以查看您当前安装的带有`-V`(大写)标志的所有版本。

```
$ /usr/libexec/java_home -V
Matching Java Virtual Machines (2):
    12, x86_64: "OpenJDK 12"    /Library/Java/JavaVirtualMachines/jdk-12.jdk/Contents/Home
    11, x86_64: "OpenJDK 11"    /Library/Java/JavaVirtualMachines/jdk-11.jdk/Contents/Home

/Library/Java/JavaVirtualMachines/jdk-12.jdk/Contents/Home 
```

同时，`-v`(小写)标志将返回到所述 JVM 的路径，然后可以将其设置为`JAVA_HOME`环境变量。`JAVA_HOME`设置后，优先于`/usr/libexec/java_home`的默认顺序。

```
$ export JAVA_HOME=$(/usr/libexec/java_home -v 11)
$ java --version
openjdk 11 2018-09-25
OpenJDK Runtime Environment 18.9 (build 11+28)
OpenJDK 64-Bit Server VM 18.9 (build 11+28, mixed mode) 
```

想重温 JDK 1.6 的太平日子吗？往前走。玩得开心。不，我很好-我会留在这里，干杯。当然，在您的 shell 中运行`export`命令只会持续该 shell 实例的生命周期。为了更长久的解决办法，你需要咬咬你的`.bashrc` / `.zshrc`或者类似的东西。

我假设`/usr/libexec/java_home`的默认选择反映了当您在`/System/Library/Frameworks/JavaVM.framework/`中运行一个存根应用程序时会发生什么。或者，您可能已经发现在前面的`JavaVM.framework`目录中也有一个`java_home`二进制文件。稍等...

```
$ greadlink -f /usr/libexec/java_home
/System/Library/Frameworks/JavaVM.framework/Versions/A/Commands/java_home 
```

听着，我*有没有*警告过你那会很麻烦。但至少现在我们对 Java 如何在我们的系统上运行有了更多的了解！

* * *

在下一篇文章中，我们将开始探讨编译和运行我们的第一个 Java 应用程序所面临的挑战。当这个帖子上线时，我会用一个链接来更新它！

这篇文章对你有用吗？我真的很感谢任何评论和反馈，是否有任何事情可以做得更清楚或解释得更好。我也非常感谢任何更正！