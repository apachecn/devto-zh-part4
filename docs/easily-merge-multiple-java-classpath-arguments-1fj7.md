# 轻松合并多个 Java 类路径参数

> 原文：<https://dev.to/awwsmm/easily-merge-multiple-java-classpath-arguments-1fj7>

我总是搞不清`java`和`jshell`的正确标志是`-cp`还是`--classpath`还是`--class-path`还是其他什么，所以我没有像正常人一样努力让*记住*的某些东西，而是花了几个小时把这个脚本拼凑起来。

它自动检测一系列命令行参数中的`cp`、`classpath`和`class-path`标志的任何变化，并将所有正确的参数连接起来，给你一个漂亮、干净的`classpath`。希望有用！