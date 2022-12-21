# 多个 JDK？没问题！

> 原文：<https://dev.to/sandrogiacom/multiple-jdks-no-problem-1knc>

你的机器上有几个版本的 Java 出现过问题吗？您是否浪费时间配置环境变量？在 Linux 世界中有一个优雅的解决方案，它被称为 **jEnv** 。

# 动机

你需要维护你用 Java 8 甚至更低版本编写的 Java 程序。用 Java 11 开发应用，仍然尝试 Java 12 或 13。当然你会有问题。

# 场景

假设你已经有了一个 Java 8 和 Maven 的开发环境。在示例中，我将使用 Linux Mint。当我们在同一台机器上有多个版本的 Java 时，出现问题是很常见的。为了解决这个问题，我们最终使用 alternatives 框架编写了几个脚本来更改 JAVA_HOME，并且我们经常需要重启用户会话以使更改生效。

# 用 JDK 8 安装 OpenJDK 11

这个想法是保留你的 Java 的当前版本，安装 JDK 11 并根据项目需要运行一个或另一个版本。我们将看到用一些工具来管理它是多么容易。当我提到 Java 11、JDK 或 OpenJDK 时，我总是指 OpenJDK 11。

## 下载 OpenJDK:

```
$ wget https://download.java.net/java/GA/jdk11/13/GPL/openjdk-11.0.1_linux-x64_bin.tar.gz
$ sudo tar xvf openjdk-11.0.1_linux-x64_bin.tar.gz --directory /usr/lib/jvm/ 
```

之后，如果您运行命令`java -version`，您将会看到什么都没有改变，我们继续使用 Java 8:

```
$ java -version
java version "1.8.0_191" Java(TM) SE Runtime Environment (build 1.8.0_191-b12)Java HotSpot(TM) 64-Bit Server VM (build 25.191-b12, mixed mode) 
```

Maven 还在用现在的 Java:

```
$ mvn -version
Apache Maven 3.5.4 (1edded0938998edf8bf061f1ceb3cfdeccf443fe; 2018-06-17T15:33:14-03:00) Maven home: /opt/maven Java version: 1.8.0_191, vendor: Oracle Corporation, runtime: /usr/lib/jvm/java-8-oracle/jre 
```

要检查 Java 11 是否正常，只需运行命令:

```
$ /usr/lib/jvm/jdk-11.0.1/bin/java -version
openjdk version "11.0.1" 2018-10-16 OpenJDK Runtime Environment 18.9 (build 11.0.1+13) OpenJDK 64-Bit Server VM 18.9 (build 11.0.1+13, mixed mode) 
```

准备好了。我们在同一台机器上有两个版本的 Java，现在怎么办？

假设我们的大部分项目都将使用 Java 8 一段时间，让我们将这个版本定义为“全局”版本，将 Java 11 定义为“本地”版本。

# jEnv

jEnv 是一个命令行工具，帮助您忘记如何设置 JAVA_HOME 环境变量。它是一个允许您以全局或本地方式配置 Java 版本的工具。
假设我们已经安装了 Java 8，并使用它和 maven 进行了配置。

# 安装 jEnv:

该工具仅适用于 Linux 和 Mac。要在 Linux 上安装，请遵循以下步骤(假设您已经安装了 git):

```
$ git clone https://github.com/gcuisinier/jenv.git ~/.jenv 
```

```
$ echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.bash_profile
$ echo 'eval "$(jenv init -)"' >> ~/.bash_profile 
```

如果用 Zsh:

```
$ echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.zshrc
$ echo 'eval "$(jenv init -)"' >> ~/.zshrc 
```

关闭终端，然后再次打开。运行`jenv`命令

如果一切正常，您应该会看到消息`jenv` + `version`。例如:jenv 0.5.1-5-gaf89d78

如果不工作，执行步骤 2，对于 bashrc 而不是 bash_profile

```
$ echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.bashrc
$ echo 'eval "$(jenv init -)"' >> ~/.bashrc 
```

## 给 jEnv 添加 Java 版本:

Java 8:

```
$ jenv add /usr/lib/jvm/java-8-oracle 
```

Java 11:

```
$ jenv add /usr/lib/jvm/jdk-11.0.1 
```

检查 java 版本:

```
$ jenv versions
  system
  11.0
  11.0.1 (set by /home/sandro/.jenv/version)
  1.8
  1.8.0.191 
```

## 跑步

假设您需要保留 Java 8 作为遗留应用程序的默认版本，并在某些项目或目录中使用 Java 11。

定义全局版本:

```
$ jenv global 1.8.0.191 
```

定义本地版本(每个目录):

```
$ jenv local 11.0.1 
```

## 结论

我们安装 Java 11，而不损害已经使用 Java 8 的环境。我们安装并配置 jEnv 工具，生成并编译一个 Java 11 项目。在下一篇文章中，我们将分析一个 Java 8 应用程序，看看我们能否在 Java 11 中运行它。

如果你有任何疑问或问题，请发表评论。感谢阅读。

## 参考文献

[http://www.jenv.be/](http://www.jenv.be/)

[![](img/ad9b9da1674b387c7b673d8f5867bfc0.png)](https://twitter.com/sandrogiacom)