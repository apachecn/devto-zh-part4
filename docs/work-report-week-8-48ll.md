# 工作报告。第 8 周

> 原文：<https://dev.to/antoniogamiz/work-report-week-8-48ll>

欢迎新的工作报告！抱歉迟到了两天，这几天我有很多事要做。来看看这周发生了什么:d。

### P6DOC

我不确定你是否知道这个工具: [p6doc](https://github.com/noisegul/perl6-p6doc) 。它正在由我在#gsoc2019 的一个合作伙伴 Joel 进行改进。这是一个命令行界面，用于查阅 Perl6 官方文档。类似于`man`的东西，但是是针对 Perl6 的！

我本周测试了它，我认为将我的项目与该工具集成在一起以缩短咨询时间会很好。所以我把我的想法写在一期[杂志](https://github.com/noisegul/perl6-p6doc/issues/17)上，乔尔和我正在讨论这个新功能！

几天后会有新的变化(见[下一个分支](https://github.com/noisegul/perl6-p6doc/tree/next))，所以要警惕！

### GitHub 模板

我没有意识到我没有任何模板供问题或拉请求使用，所以我把它们添加到了 repository :D。我还意识到 perl6/doc 使用的是旧的模板系统，所以我提出了一个问题来更新它！关于#gsoc19，我最喜欢的事情之一是我正在学习关于`git`和`github`的大量东西。有很多功能可以帮助你发展你的想法，比如项目、里程碑等等。

### 大待办事项列表

几天前，我的导师修改了我的代码，并对这个[待办事项列表](https://github.com/antoniogamiz/Perl6-Documentable/blob/master/TODO.md)提出了很多改进建议。这有点长，但我正试图逐项完成它。

很多问题已经解决了，但是其他问题，比如并行生成文档，对我来说是无法解决的。原来`pod2html`(用来把所有的 pod 转换成 HTML)是不能并行执行的。我尽了一切努力，但毫无结果。我已经向[报告了这个问题](https://github.com/perl6/Pod-To-HTML/issues/63)，但是正如你所看到的错误是一个 segfault。所以这看起来像是拉库多的 bug 而不是模块本身。Debug 这个问题就是地狱的定义，有那么多函数被调用，几个线程等等。因此，并行生成文档将不得不等到这个错误被解决或者新的 HTML 模块被开发出来。

我还重构和删除了一些模块，以提高可读性和理解过程。我还在 [docs/reference](https://github.com/antoniogamiz/Perl6-Documentable/tree/master/docs) 下做了一个新的文档，解释这些东西是如何制作的。我这样做是因为以前的文件太专业了，人们很难看到全局。我希望通过这种方法，系统会更容易理解。

这就是我这周的工作。它看起来并不多，但是重构和 TODO 列表的一些项目已经引起了几个问题，因为一些事情是高度耦合的(我的错)。现在一切都更加模块化，更不容易出错。

### Perl6 好东东！

现在我要告诉你一些关于 perl6 的有趣的事情！如果你是一名程序员，你肯定会使用数组、列表等。Perl6 为您提供了轻松使用这些数据结构所需的一切。

我经常使用的一个函数是`grep`(参见[文档](https://docs.perl6.org/routine/grep))。这个功能就像一个过滤器。你可以按类型过滤，使用正则表达式，函数，任何你能想到的东西。让我们看一些例子。

```
say ('hello', 1, 22/7, 42, 'world').grep: Int; 
# OUTPUT: «(1 42)␤» 

say ('hello', 1, 22/7, 42, 'world').grep: Rat; 
# OUTPUT: «(3.142857)␤» 

say ('hello', 1, 22/7, 42, 'world').grep: {.Str.chars > 3};
# OUTPUT: «(hello 3.142857 world)␤» 
~~~

![simply perl6](https://thepracticaldev.s3.amazonaws.com/i/x6smko21cv1tovbchlrc.jpg)

You will see how amazing is this once you start using it.

### Closing

And that's have been everything for today. I hope you give Perl6 a try and start using these amazing features.

See you next week with a new report and a new thing about Perl6! :D. 
```

Enter fullscreen mode Exit fullscreen mode