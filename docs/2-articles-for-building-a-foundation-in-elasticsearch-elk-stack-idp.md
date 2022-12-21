# 2 篇为 Elasticsearch | ELK Stack 打下基础的文章

> 原文：<https://dev.to/full_stackgeek/2-articles-for-building-a-foundation-in-elasticsearch-elk-stack-idp>

**1。Elasticsearch 及麋鹿栈简介**
**关于本文:**
在本文中，我们讨论的是 Elasticsearch。我们将从 Elasticsearch 的介绍开始，对所谓的 ELK stack 进行简短的讨论。然后，我们将转向 Elasticsearch 的架构，以及什么是节点、集群、分片、索引、文档、复制等等。那我们开始吧。

**目录:**

*   [1。弹性搜索简介](https://fullstackgeek.blogspot.com/2019/03/introduction-to-elasticsearch-and-elk-stack.html#Introduction)
*   [2。了解弹性堆栈](https://fullstackgeek.blogspot.com/2019/03/introduction-to-elasticsearch-and-elk-stack.html#UnderstandingElasticStack)
    *   Elasticsearch(我们已经在第 1 节中介绍过了！)
    *   [Kibana](https://fullstackgeek.blogspot.com/2019/03/introduction-to-elasticsearch-and-elk-stack.html#Kibana)
    *   [日志存储](https://fullstackgeek.blogspot.com/2019/03/introduction-to-elasticsearch-and-elk-stack.html#Logstash)
    *   [节拍](https://fullstackgeek.blogspot.com/2019/03/introduction-to-elasticsearch-and-elk-stack.html#Beats)
*   [3。弹性搜索的架构](https://fullstackgeek.blogspot.com/2019/03/introduction-to-elasticsearch-and-elk-stack.html#ArchitectureofElasticsearch)
    *   [节点和集群](https://fullstackgeek.blogspot.com/2019/03/introduction-to-elasticsearch-and-elk-stack.html#NodesandClusters)
    *   [索引和文件](https://fullstackgeek.blogspot.com/2019/03/introduction-to-elasticsearch-and-elk-stack.html#IndicesandDocuments)
    *   [分片](https://fullstackgeek.blogspot.com/2019/03/introduction-to-elasticsearch-and-elk-stack.html#Sharding)
    *   [复制](https://fullstackgeek.blogspot.com/2019/03/introduction-to-elasticsearch-and-elk-stack.html#Replication)
        *   [保持副本同步](https://fullstackgeek.blogspot.com/2019/03/introduction-to-elasticsearch-and-elk-stack.html#KeepingReplicasSynchronized)
*   [4。如何执行搜索查询(快速概述)](https://fullstackgeek.blogspot.com/2019/03/introduction-to-elasticsearch-and-elk-stack.html#HowSearchHappens)
*   [5。结束注释](https://fullstackgeek.blogspot.com/2019/03/introduction-to-elasticsearch-and-elk-stack.html#EndNotes)
*   [6。推荐文章](https://fullstackgeek.blogspot.com/2019/03/introduction-to-elasticsearch-and-elk-stack.html#RecommendedArticles)

**文章链接:**[elastic search 与麋鹿栈简介](https://fullstackgeek.blogspot.com/2019/03/introduction-to-elasticsearch-and-elk-stack.html) 

**2。将现有架构升级到 elastic search | ELK Stack** **关于本文:**
假设我们有一个运行在 web 服务器上的电子商务应用程序。数据存储在数据库中，例如产品类别和产品本身。因此，当请求产品页面时，web 应用程序在数据库中查找产品，呈现页面，并将其发送回访问者的浏览器。这就是常见的客户机-服务器-数据库体系结构的样子。

**目录:**

*   [现有架构](https://fullstackgeek.blogspot.com/2019/07/upgrading-architecture-to-elasticsearch.html#ExistingArchitecture)
*   [逐步升级到 Elasticsearch/ELK Stack 的方法](https://fullstackgeek.blogspot.com/2019/07/upgrading-architecture-to-elasticsearch.html#Upgrading)
    *   [【第一步】——添加弹性搜索](https://fullstackgeek.blogspot.com/2019/07/upgrading-architecture-to-elasticsearch.html#AddElastic)
    *   [【第二步】——添加基巴纳](https://fullstackgeek.blogspot.com/2019/07/upgrading-architecture-to-elasticsearch.html#AddKibana)
    *   [【步骤 3】——添加 Metricbeat 和 Filebeat](https://fullstackgeek.blogspot.com/2019/07/upgrading-architecture-to-elasticsearch.html#AddBeats)
    *   [【第四步】——添加日志存储](https://fullstackgeek.blogspot.com/2019/07/upgrading-architecture-to-elasticsearch.html#AddLogstash)
    *   [结论](https://fullstackgeek.blogspot.com/2019/07/upgrading-architecture-to-elasticsearch.html#Conclusion)
*   [推荐文章](https://fullstackgeek.blogspot.com/2019/07/upgrading-architecture-to-elasticsearch.html#Reco)

**文章链接:** [将现有架构升级为 Elasticsearch | ELK Stack](https://fullstackgeek.blogspot.com/2019/07/upgrading-architecture-to-elasticsearch.html) 

喜欢这个博客吗？点击这里订阅[，不要错过任何未来的博客文章](http://eepurl.com/gveQr5)