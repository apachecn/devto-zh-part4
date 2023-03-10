# 关于大数据应用的高级数据结构和算法的书

> 原文：<https://dev.to/gakhov/book-on-advanced-data-structures-and-algorithms-for-big-data-applications-19m2>

在这里，我想介绍我几个月前出版的关于数据密集型应用的高级算法的书，名为“大数据应用中的概率数据结构和算法”(ISBN: 9783748190486)。关于这本书的详细信息你可以在[https://pdsa.gakhov.com](https://pdsa.gakhov.com)或[亚马逊](https://www.amazon.com/dp/3748190484)找到，下面我给你介绍一下这本书的主题。

## 什么是概率数据结构，我们为什么需要它们？

您可能已经知道，大数据不仅仅是大小的问题。大数据的数据集更大、更复杂、生成速度更快，超出了我们当前资源的处理能力。这样的数据集如此之大，以至于传统的数据处理软件和算法根本无法管理它们。这就是为什么**大数据不是指数据，是指技术**。因此，为了成功地解决这些问题，我们需要考虑如何改进/改变我们使用的技术。其中一个例子是使用与经典算法完全不同的高级算法。

*概率数据结构和算法* (PDSA)是一系列高级技术，针对使用固定或次线性内存和恒定执行时间进行了优化；它们通常基于散列法，并具有许多其他有用的特性。然而，它们也有一些缺点，例如它们不能提供精确的答案，并且有一定的误差概率(这实际上是可以控制的)。误差和资源之间的权衡是区分该家族的算法和数据结构的另一个特征。

这种技术很自然地找到了它们在大数据中的用例，因为我们也有权衡——要么让整个数据未经处理，要么同意一些结果不完全准确。

我想，每个人都听说过大数据的三个 v、量、速度和多样性*。这些维度意味着有效的成员查询、基数估计、数据流的频率和等级计算以及相似性计算等问题。举个例子，我们可以通过解决*大数据的 3v*来指出 PDSA 家族的一些算法。*

[![Probabilistic data structures and algorithms in Big Data Ecosystem](img/e83cec2f7d2b69c441528480ed8596ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uTZqYq3D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.gakhov.com/static/wimg/articles/kdnuggets/pdsa-in-big-data.jpg)

您可能从未听说过其中的大部分，但是这样的数据结构不一定是新的。例如，最广为人知的数据结构是 70 年代设计的*布隆过滤器*。它有效地解决了在不需要存储所有元素的情况下在恒定时间内执行成员查询(决定某个元素是否属于数据集的任务)的问题。这是一个概率数据结构的例子，但是在许多领域还有更多为各种任务设计的例子。

这种数据结构已经在许多流行和知名的产品中实现，如 Google Big Query、Amazon Redshift、Redis、Cassandra、CouchDB、Elasticsearch 和许多其他产品。

> ![Andrii Gakhov profile image](img/ac713784ac3f791096e6897d6bc7e2c3.png)Andrii gak hov@ gak hov![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)当你对某个分区发出读请求时，Apache [@cassandra](https://twitter.com/cassandra) 必须合并内存中的数据和磁盘上的数据。为了避免加载每个数据文件，它使用了众所周知的[#概率](https://twitter.com/hashtag/probabilistic)数据结构，如 Bloom filter(具有 0.1%的误报概率)[# PDS book](https://twitter.com/hashtag/PDSABook)[cassandra.apache.org/doc/latest/ope…](https://t.co/AhdRLhpRv0)08:35AM-09 2019[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1115533650773315585)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1115533650773315585)1[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1115533650773315585)0

例如，您可以在 Spark SQL 中使用`approx_count_distinct`命令，或者在 Redis 中使用`PFCOUNT`命令，这些命令实际上是由*超对数*算法提供支持的，这是 PDSA 的另一个例子。

[![HyperLogLog in Redis](img/91c7d4965771f34ff22c7b5a00358d6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YTOZn1Ws--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.gakhov.com/static/wimg/articles/kdnuggets/hll-in-redis.jpg)

## 书的组织

这本书由六章组成，每一章之前都有一个介绍，然后是一个简短的总结和与该章相关的进一步阅读的参考书目。每章都致力于大数据应用中的一个特定问题，它从对问题的深入解释开始，然后介绍可用于有效解决问题的数据结构和算法。

第一章简要概述了概率数据结构中广泛使用的流行的**哈希函数**和哈希表。第 2 章致力于**近似成员关系**查询，这是这种结构最广为人知的用例。在第 3 章中，讨论了有助于估计唯一元素的**数量的数据结构。第 4 章和第 5 章致力于流媒体应用中与重要的**频率和等级**相关的度量计算。第 6 章包括解决**相似性**问题的数据结构和算法，特别是最近邻搜索。**

[![Organization of the book](img/753fa070df08fb1e5db1649810398ed6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mBrVD9Nh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vw00h1eghascgnioemdu.jpg)

## 巨蟒

如果您对在代码中使用这种数据结构感兴趣，几乎所有编程语言中都有大量的实现。作为这本书的一个副作用，我创建了一个 Python 库 PDSA，[https://github.com/gakhov/pdsa](https://github.com/gakhov/pdsa)，它是在 Cython 中实现的，可以很容易地从任何 Python3 驱动的应用程序中使用。顺便说一句，欢迎大家投稿！

如果你想联系我，可以通过 Twitter [@gakhov](https://twitter.com/gakhov) 联系，或者在 https://pdsa.gakhov.com[访问这本书的网页](https://pdsa.gakhov.com)