# 子流程的命令执行技巧-设计 CI/CD 系统

> 原文：<https://dev.to/tryexceptpass/command-execution-tricks-with-subprocess-designing-ci-cd-systems-5fg3>

任何持续集成过程中最关键的步骤是执行构建指令并测试其输出。实现这一步有无数种方法，从简单的 shell 脚本到复杂的任务系统。

遵循简单和实用的原则，今天我们将继续讨论关于[设计 CI/CD 系统的系列文章](https://tryexceptpass.org/designing-continuous-build-systems)以及我们的执行脚本的实现。

本系列的前几章已经建立了要实现的[构建指令](https://tryexceptpass.org/article/continuous-builds-parsing-specs/)。它们涵盖了构建规范文件的格式和位置。以及它运行的 [docker 环境](https://tryexceptpass.org/article/continuous-builds-docker-swarm)及其局限性。

## 使用子流程执行

YAML 规范文件中提供的大多数指令都是 shell 命令列表。因此，让我们看看 Python 的[子流程](https://docs.python.org/3/library/subprocess.html)模块如何在这种情况下帮助我们。

我们需要执行一个命令，等待它完成，检查退出代码，并打印任何输出到 stdout 或 stderr。我们可以在`call()`、`check_call()`、`check_output()`和`run()`之间进行选择，所有这些都是围绕一个低级`popen()`函数的包装器，可以提供更细粒度的过程控制。

这个`run()`函数是 Python 3.5 中最近增加的。它提供了我们正在寻找的必要的执行、阻塞和检查行为，每当它发现一个失败时就引发一个`CalledProcessError`异常。

同样值得注意的是， [shlex](https://docs.python.org/3/library/shlex.html) 模块是一个免费的库，它提供了一些实用程序来帮助您进行子进程调用。它提供了一个`split()`函数，这个函数足够智能，可以在给定命令行字符串的情况下正确格式化列表。以及`quote()`帮助*逃离*外壳命令，避免外壳注入漏洞。

## 安全注意事项

思考一分钟，意识到你正在编写一个运行第三方编写的命令行指令的执行系统。这有很大的安全隐患，也是为什么大多数在线构建服务不允许您深入到这个细节层次的主要原因。

那么，我们能做些什么来降低风险呢？

[读下去...](https://tryexceptpass.org/article/continuous-builds-subprocess-execution/)