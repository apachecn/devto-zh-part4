# 数据库版本/未来方向

> 原文：<https://dev.to/johanneslichtenberger/version-your-database-future-directions-b8h>

大家好，

因为我想尽快发布 SirixDB[1]的 1.0.0 版本，但遗憾的是缺少一个开源社区，所以我想在这里讨论一下你认为对未来方向最重要的是什么。

简而言之，SirixDB 通过一个巨大的完全基于写时复制的索引-trie 结构来保存数据库中每个资源的历史。这意味着它在修订之间共享未更改的数据库页。SirixDB 允许复杂的时间旅行查询并实现不同的算法。它以二进制格式存储 XML 和 JSON，但也可以存储图表或其他类型的数据。

未来的想法是:

*   水平扩展，即通过单个主机写入，提供读取自己的写入一致性，在几个集群节点上复制资源...最有可能使用 ZooKeeper 和 Apache BookKeeper，并带有一次交付语义...
*   资源修订版之间差异的交互式可视化。SirixDB 目前以二进制格式存储树结构数据，即 XML 和 JSON。不同的能力已经存在。还有一些过时的可视化处理，我想把它们移植到 D3 的网页上。此外，一个网络界面会很好
*   添加基于成本的查询优化器规则和索引重写规则，以显著提高查询性能
*   研究如何巧妙地删除旧版本(我必须查找 ZFS 是如何允许删除快照的)。然而，作为一种丑陋的黑客行为，后台进程可以将最新的版本复制到一个新的资源中。我想这变得有点棘手，因为未更改的数据库页面在修订版之间共享，记录页面甚至被版本化。因此，根据所使用的算法，需要从不同版本的页面片段中重建页面。

此外，我想完成整个数据库的版本控制，而不仅仅是数据库中的资源。

直到最近，我一直在考虑横向扩展，将 GraalVM 用于本机映像，即在 docker 容器中提供超快的启动时间，从 Bookkeeper 集群中进行读写操作，并将一切部署到 Kubernetes 集群。

但是，也许用漂亮的交互式可视化展示可能会得到更多的关注，我认为对我来说，学习前端的东西也很棒。由于完全缺乏用户，它也可能更有用，因此它只是从工程的角度来看才真正有趣；-)

约翰尼斯，祝你周末愉快

[1] [https://sirix.io](https://sirix.io) 和[https://github.com/sirixdb/sirix](https://github.com/sirixdb/sirix)
【2】[https://m.youtube.com/watch?feature=youtu.be&v = 19 cxxbkl 5 VI](https://m.youtube.com/watch?feature=youtu.be&v=l9CXXBkl5vI)