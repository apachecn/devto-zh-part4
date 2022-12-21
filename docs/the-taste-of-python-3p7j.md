# 蟒蛇的味道

> 原文：<https://dev.to/xni/the-taste-of-python-3p7j>

终于是星期五了！又一周过去了，回顾过去，我可以看到它又一次被花费在令人疲惫的代码审查上。至少对我来说，这是一个非常耗时耗力的过程。以及人们通常所说的会更好。但是我没有看到任何改善。我仍然会在每 600 行的 PR 中留下大约 200 条评论。我的同事非常聪明，他们在不同的计算机科学领域都有经验，从编译器到 FPGA，从国际咨询到量子计算机，但是我们仍然在代码审查上花费了太多的时间。

因此，作为实现减少请求提交和合并之间时间的目标的第一步，我们将 [lint-review](https://github.com/markstory/lint-review) 连接到 GitHub repo。这很有帮助，但没有我希望的那么多。

我在想我们可以做些不同的事情来使我们的过程更好更快。而我想到的是“一个味道”。在你尝试过米其林餐厅后，你不会忘记它。在你驾驶宝马跑车后，你可能还会继续使用你的 13 年前的大众汽车，但你知道有更多的东西。我对酒精一无所知，但一些稀有的葡萄酒和你在特易购以每瓶 4 英镑买到的葡萄酒之间可能存在差异。

于是，我被伟大代码的味道毒害了。幸运的是，我和一些优秀的工程师一起工作，他们教会了我一些关于好代码的知识。我对伟大的定义是什么？

1.  代码应该就在你期望的地方。
2.  它应该尽可能简单，但不能少于要求。
3.  作者并不是想证明他们比读者聪明。他们不认为代码是自我表达的工具，而是创造某种工作的工具。
4.  代码应该以这样一种方式编写，即你不能确切地分辨出是谁写的。你知道成为一名[御林军](https://en.wikipedia.org/wiki/Yeomen_Warders)需要多长时间吗？在军队服役 22 年。过了这样一段时间，无论如何你都可能会像一个人一样。

所以，话虽如此，我已经决定开始做一些不同的事情。因为我在乎。因为我希望我们作为一个团队取得成功。老实说，我也想成为一名开发人员和架构师，而不是一遍又一遍地指出类似的问题。

所以，现在我将开始一系列的文章来展示好的代码是怎样的。希望它能帮助你发展心态。

我想我会写一系列的帖子，回顾不同的项目，强调一些重要的细节。

今天我们将从 CPython 标准库开始。我认为这是你能找到的最高质量的 Python 代码，如果有任何疑问，我会推荐使用它作为真理的无条件来源和仲裁者。

让我们来看看日志适配器。希望您已经熟悉了记录器、处理程序和格式化程序。LoggerAdapter 是一个不太知名的类。因此，我们将阅读源代码，决定它到底在做什么，而不阅读 pep 和文档。

开始吧！

所以，首先要找到源头。首先是 https://github.com/python/cpython/。顶层有许多文件夹。

```
$ ls
CODE_OF_CONDUCT.md LICENSE            Misc               PCbuild            README.rst         config.sub         m4
Doc                Lib                Modules            Parser             Tools              configure          pyconfig.h.in
Grammar            Mac                Objects            Programs           aclocal.m4         configure.ac       setup.py
Include            Makefile.pre.in    PC                 Python             config.guess       install-sh 
```

我们的代码在哪里？大概在`Lib`或者`Modules`吧，大家怎么看？`ls Lib`，`ls Modules`说得很清楚。最终，我们导航到了`Lib/logging`文件夹。我们的适配器在哪里？

```
$ ls
__init__.py config.py   handlers.py 
```

我们越来越近了。在我们开始阅读代码之前，有一个小小的评论。`logging`是一个相当老的模块，可追溯到 2001 年:

```
commit 57102f861d506b6c2d2215d100dac9143574fa77
Author: Guido van Rossum <guido@python.org>
Date:   Wed Nov 13 16:15:58 2002 +0000

    Adding Vinay Sajip's logging package.

diff --git a/Lib/logging/__init__.py b/Lib/logging/__init__.py new file mode 100644
index 0000000000..e0f2de5dd2 --- /dev/null +++ b/Lib/logging/__init__.py @@ -0,0 +1,1183 @@ +#! /usr/bin/env python
+#
+# Copyright 2001-2002 by Vinay Sajip. All Rights Reserved. 
```

正因为如此，风格可能与您的预期有所不同，但是请记住，从 Python 2 到 3 的迁移已经是一件痛苦的事情，因此，破坏与更多模块的兼容性可能不是一个好主意。

好了，回到我们最初的话题。假设有人告诉我们有`logging.LoggerAdapter`类，我们想更好地理解它。

```
class LoggerAdapter(object):
    """  
    An adapter for loggers which makes it easier to specify contextual
    information in logging output.
    """

    def __init__(self, logger, extra):
        """  
        Initialize the adapter with a logger and a dict-like object which
        provides contextual information. This constructor signature allows
        easy stacking of LoggerAdapters, if so desired.

        You can effectively pass keyword arguments as shown in the
        following example:

        adapter = LoggerAdapter(someLogger, dict(p1=v1, p2="v2"))
        """
        self.logger = logger
        self.extra = extra 
```

从这里我们能看到什么？

这是一个很小的类，它有两个字段。它使一些代码变得更容易，但并不期望带来新的功能。不知何故，适配器可以嵌套。

```
def debug(self, msg, *args, **kwargs):
    """
    Delegate a debug call to the underlying logger.
    """
    self.log(DEBUG, msg, *args, **kwargs)

def info(self, msg, *args, **kwargs):
    ... 
```

因此，适配器具有与普通记录器相同的方法集。但是我们没有使用继承。这是一个非常明智的决定。我们正在编写一个适配器。所以我们想要实现期望的接口，但是重写行为。鸭子打字是我们这里的朋友。如果在某个时候`self.logger`的底层实现将会改变，我们的适配器将会抛出`AttributeError`异常，而不是在我们选择继承的情况下回到最初的非修饰实现。

跳到下一个功能:

```
def log(self, level, msg, *args, **kwargs):
    """
    Delegate a log call to the underlying logger, after adding
    contextual information from this adapter instance.
    """
    if self.isEnabledFor(level):
        msg, kwargs = self.process(msg, kwargs)
        self.logger.log(level, msg, *args, **kwargs) 
```

所以这里我们只是将所有东西代理到底层的`self.logger`，但是修补了`msg`和`kwargs`。

```
def process(self, msg, kwargs):
    """
    Process the logging message and keyword arguments passed in to
    a logging call to insert contextual information. You can either
    manipulate the message itself, the keyword args or both. Return
    the message and kwargs modified (or not) to suit your needs.

    Normally, you'll only need to override this one method in a
    LoggerAdapter subclass for your specific needs.
    """
    kwargs["extra"] = self.extra
    return msg, kwargs 
```

所以，这是`LoggerAdapter`的核心。看，多体贴啊。即使我们没有修补`msg`参数，我们仍然为从`LoggerAdapter`派生的类保留了更新它的空间。另一个有趣的选择是，从代码中可以清楚地看到，即使`kwargs`有一个`extra`参数，我们也要覆盖它。我也喜欢这个论点的名字，`kwargs`。它不是一个`**kwargs`，它只是一个通常的字典参数，但因为这个名字，我觉得我们在原地修改它是可以的。

然后还有一堆其他的方法，比如:

```
def setLevel(self, level):
    """
    Set the specified level on the underlying logger.
    """
    self.logger.setLevel(level) 
```

同样，可以通过使用继承来避免吗？是的，有可能。但是，我们正在失去控制，`LoggerAdapter`不再是一个适配器。此外，目前，`LoggerAdapter`与任何遵循协议的记录器兼容(有调试/信息/警告和一些其他方法)，它实际上并没有扩展`logging.Logger`方法，它可以使用你自己的记录器作为底层记录器。这也增加了这个类的价值。

就是这个！仅仅通过少读 5 分钟的代码，我们就得到了我们想知道的一切。让我们试着使用它！

```
>>> import sys
>>> import logging
>>> logging.basicConfig(
        stream=sys.stdout,
        format='%(asctime)s %(req_id)s %(message)s',
        level=logging.DEBUG)
>>> logging.info('Hello', extra={'req_id': '123123'})
2019-06-08 00:41:24,420 123123 Hello
>>> my_logger = logging.LoggerAdapter(
        logging.getLogger(), {'req_id': '123123'})
>>> my_logger.info('world!')
2019-06-08 00:42:28,724 123123 world!
>>> my_logger.info('world!', extra={'req_id': 999999})
2019-06-08 13:27:35,105 123123 world! 
```

代码清楚吗？是啊！很啰嗦吗？是的，它是。但是正因为如此，没有人会真正理解或扩展这个类。请打开文件再次阅读定义:[https://github . com/python/cpython/blob/master/Lib/logging/_ _ init _ _。py#L1745](https://github.com/python/cpython/blob/master/Lib/logging/__init__.py#L1745)

但是，这里的重点不是再写一个 Python 类，它要宽泛得多:请看看漂亮的代码。我们都知道，美丽是不会错的。

所以，请阅读好的代码。它和读书一样重要。这是所有实际应用的最佳实践。即使周围没有好的例子，因为我们正在进行绿地项目，即使我们的一些内部图书馆甚至没有那样接近，现在你知道我的期望是什么了。我希望在我的一系列文章之后，你不会再以这种风格写作了。