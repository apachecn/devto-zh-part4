# 大数据的内存性能

> 原文：<https://dev.to/johanneslichtenberger/in-memory-performance-for-big-data-4id6>

这篇文章非常有趣——使用缓冲区管理器对传统数据库系统进行指针重组，以便与内存中的数据库竞争较小的数据集:

[http://www.vldb.org/pvldb/vol8/p37-graefe.pdf](http://www.vldb.org/pvldb/vol8/p37-graefe.pdf)

似乎我“意外地”为[https://sirix.org](https://sirix.org)实现了类似的机制，因为我在缓存未命中时设置内存中的页面引用，并且一旦缓冲区管理器必须驱逐条目，就显式地将它们设置为空:-)然而，由于我使用对象实例作为键，我可以简单地将引用设置为空，而不是使用另一个缓存进行子到父的映射。我将看看是否可以简单地在一个实例字段中缓存 Java hashCode 值，以进一步提高一点性能。

周末愉快:-)

子目〔t0〕约翰尼斯