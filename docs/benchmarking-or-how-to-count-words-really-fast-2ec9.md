# 基准测试或“如何快速统计单词”

> 原文：<https://dev.to/mrcryn/benchmarking-or-how-to-count-words-really-fast-2ec9>

一个同事给我看了这个有趣的 GitHub 库:[github.com/juditacs/wordcount](https://github.com/juditacs/wordcount)。

自述文件解释了什么是字数统计挑战:

> 任务是分割文本并计算每个单词的频率，然后打印按频率降序排列的列表。领带是按字母顺序印刷的。

@juditacs 做了一件伟大的工作，用几种编程语言聚集了不同的解决方案，创建了一个 Dockerfile 文件，可以很容易地根据匈牙利维基百科文本转储设置和测试所有程序。他们甚至写了两篇关于字数统计挑战的博文。 [1](http://juditacs.github.io/2015/11/26/wordcount.html) ， [2](http://juditacs.github.io/2016/03/19/wordcount2.html)

自述文件的最新基准测试于 2016 年 6 月 13 日完成:

| 军阶 | 实验 | CPU 秒 | 用户时间 | 捐助者 |
| --- | --- | --- | --- | --- |
| one | Java-Xms2G-Xmx8G-class path Java:Java/zah-0.6 . jar word count optimized | One hundred and forty point six four | One hundred and ninety-one point four four |  |
| Two | Java-Xms2G-Xmx8G-class path Java:Java/trove-3 . 0 . 3 . jar word count optimized | One hundred and eighty-three point one seven | Two hundred and nineteen point three nine |  |
| three | rust/字数/字数 | Two hundred and eight point eight six | One hundred and ninety-three point three four | 约书亚·霍尔默 |

我发现非常有趣的一点是，java 解决方案是提交速度最快的一个。它比通常怀疑的 C、C++和 Rust 程序要快。

这激起了我的兴趣！为什么 Java 和 rust 程序差不多？为什么 [java 基线解决方案](https://github.com/juditacs/wordcount/blob/6314ed37792eec9b7bb86ef20b8b5d0e5a5b87cb/java/WordCountBaseline.java)与[优化解决方案](https://github.com/juditacs/wordcount/blob/6314ed37792eec9b7bb86ef20b8b5d0e5a5b87cb/java/WordCountOptimized.java)差别如此之大？而如何才能让 Rust 版本和 Java 解决方案一样快，甚至更快？

幸运的是，优化 java 解决方案的作者留下了一些[有用的评论](https://github.com/juditacs/wordcount/blob/6314ed37792eec9b7bb86ef20b8b5d0e5a5b87cb/java/WordCountOptimized.java#L11-L25):

> 关键技巧:
> 
> 1.  更快的散列
> 2.  缓冲 I/O，或者使用缓冲的 I/O 类，或者通过显式地将块读取到数组中
> 3.  将循环令牌与单个令牌分开处理(大幅提高性能)
> 4.  直接处理原始字节(对 UTF 8 安全)，以减少内存和避免文本解码成本

使用同样的技术，我们应该可以将铁锈溶液提升到顶部。

在我们开始之前，让我们先看看基线 rust 实现。这是约书亚·霍尔默提交的铁锈解决方案；我为刚接触 rust 的人添加了一些评论。