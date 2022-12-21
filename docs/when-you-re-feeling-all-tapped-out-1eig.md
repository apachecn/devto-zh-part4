# 当你感到筋疲力尽的时候

> 原文：<https://dev.to/mblayman/when-you-re-feeling-all-tapped-out-1eig>

好的软件实践说你应该做自动化测试，但是当你在一个得不到好的测试工具的环境中工作时，你该怎么做呢？使用简单的测试协议，如 TAP。

TAP 是*测试任何东西协议*。该协议非常简单，您可以编写代码在几分钟内完成 TAP 输出。这是一个基于行的协议，这意味着任何具有某种打印方法的语言都可以生成 TAP。这基本上是所有常用的软件语言。

我可以提供规则，但举个例子会更简单。

```
# This is a comment because it starts with a hash symbol. ok 1 Something passed here.
not ok 2 Whoops, there was a failure.
When there is a line that TAP does not understand (like this one), it will skip it.
# TAP files should have a plan in the format of "1..<some number of tests>". 1..2 
```

协议的关键是`ok`和`not ok`。当行以这两个短语中的一个开始时，TAP 读者会将其解释为通过或失败。除了测试号，其他的都是额外的。加上运行了多少测试，您就有了功能齐全的 TAP 输出。我们可以用 Perl 恰如其名的`prove`工具来证明这一点。

```
matt@eden:~$  prove sample.tap
sample.tap .. Failed 1/2 subtests

Test Summary Report
-------------------
sample.tap (Wstat: 0 Tests: 2 Failed: 1)
  Failed test:  2
Files=1, Tests=2,  0 wallclock secs ( 0.01 usr +  0.00 sys =  0.01 CPU)
Result: FAIL 
```

一旦你从自己开发的测试套件中获得了一些 TAP 结果，你就可以将它们放入 TAP 阅读器中，如 [Jenkins 插件](https://wiki.jenkins-ci.org/display/JENKINS/TAP+Plugin)或特定的
工具，如 [Tapper](http://tapper.github.io/Tapper/) 。

还有一些我没有在这里讨论的特性(比如 TODO
跟踪)。更多详情见 [TAP 网站](http://testanything.org/)。更有冒险精神的人可以通过查看 Perl 的 CPAN 上的事实上的解析器 [TAP::Parser](http://search.cpan.org/~ovid/Test-Harness-3.28/lib/TAP/Parser.pm) 来了解细节。

这篇文章最初发表在 mattlayman.com 的上。