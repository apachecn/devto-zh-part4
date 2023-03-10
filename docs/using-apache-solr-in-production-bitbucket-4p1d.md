# 在生产中使用 Apache Solr—bit bucket

> 原文：<https://dev.to/atlassian/using-apache-solr-in-production-bitbucket-4p1d>

### 在生产中使用 Apache Solr—bit bucket

这是由 Womaniya 后端工程师 Pulkit Kedia 的客座博文。

Solr 是建立在 Apache Lucene 之上的搜索引擎。Apache Lucene 使用倒排索引来存储文档(数据),并通过 Java API 为您提供搜索和索引功能。然而，要使用像全文这样的特性，你需要用 Java 编写代码。

Solr 是 Lucene 搜索的更高级版本。它提供了更多的功能，并且是为可扩展性而设计的。Solr 带有分页、排序、分面、自动建议、拼写检查等功能。此外，Solr 对数字和日期数据类型使用 trie 结构，例如，有一个普通的 **int** 字段和另一个表示 trie int 字段的 **tint** 字段。

Solr 的文本搜索/分析速度非常快，这归功于它的倒排索引结构。如果您的应用程序需要大量的文本搜索，Solr 是一个不错的选择。像网飞、威瑞森、美国电话电报公司和高通这样的公司使用 Solr 作为他们的搜索引擎。就连 AWS 的搜索引擎服务 Amazon Cloudsearch 也在内部使用 Solr。

本文提供了一种在生产中部署 Solr 的方法，并讨论了如何创建 Solr 集合。如果你刚刚开始使用 Solr，你应该从构建一个 Solr 核心开始。核心是单节点 Solr 服务器，没有分片和副本，而集合由各种分片及其副本组成，它们就是核心。

### 实现

在分布式搜索中，集合是跨多个服务器的逻辑索引。每台服务器上运行集合的部分称为核心。所以在非分布式搜索中，核心和集合是相同的，因为只有一个服务器。

在生产中，您需要实现一个集合，而不是一个 Solr 核心，因为核心无法保存生产数据(除非您进行垂直伸缩)。Apache Zookeeper 帮助创建跨多个服务器的连接。

有两种方法可以设置它:

1.  多个 solr 服务器，并在其中一个服务器上使用 Zookeeper
2.  Zookeeper 在不同的服务器上，所有其他 Solr 服务器都连接到它

我们将使用第二种方法来完成实施过程。第一种方法类似于第二种方法，但后者更具可扩展性。

### 安装 Solr

生成 3 个服务器，并在 2 个服务器上安装 solr(注意:您可以生成任意数量的 Solr 服务器，我们在示例中使用 3 个)。要安装 Solr，首先需要安装 Java，然后下载想要的版本并解压。

安装:wget[http://archive . Apache . org/dist/Lucene/Solr/8 . 1 . 0/Solr-8 . 1 . 0 . tgz](http://archive.apache.org/dist/lucene/solr/8.1.0/solr-8.1.0.tgz)

```
Untar: tar -zxvf solr-8.1.0.tgz 
```

你可以用 **bin/solr start** 进入 **/home/ubuntu/solr-8.0.0** 文件夹或者用**进入 bin 文件夹启动 Solr。/solr 开始**。这将在端口 8983 上启动 solr，您可以在浏览器中测试它。

重复完全相同的步骤，在第二台服务器上安装 Solr。

还记得在/etc/hosts 中设置每个 IP 和名称的列表

例如:

```
IPv4 Public IP-solr-node-1 solr-node-1 IPv4 Public IP-solr-node-2 solr-node-2 IPv4 Public IP-zookeeper-node zookeeper-node 
```

### 安装 Zookeeper

现在，第三个服务器将只需要 zookeeper，您可以将配置集推送到它。

安装:wget[https://archive . Apache . org/dist/zookeeper/zookeeper-3 . 4 . 9/zookeeper-3 . 4 . 9 . tar . gz](https://archive.apache.org/dist/zookeeper/zookeeper-3.4.9/zookeeper-3.4.9.tar.gz)

```
Untar : tar -zxvf zookeeper-3.4.9.tar.gz 
```

如果您愿意，可以将 zookeeper 的路径添加到 bashrc 文件中。

接下来，在 zookeeper-3.4.9 文件夹中，有一个 zookeeper->**zoo _ sample . CFG**自带的示例配置文件。在 path 中复制这个文件，并将其重命名为 **zoo.cfg** 。配置文件包含各种参数，如“ **dataDir** ”指定存储内存数据库快照和事务日志的目录，“ **maxClientCnxns** ”限制客户端连接的最大数量等。

打开 zoo.cfg 文件，取消“**auto purge . snap retain count = 3**”和“**auto purge . purge interval = 1**”的注释，编辑“ **dataDir = data**

接下来开始动物园管理员。

```
bin/zkServer.sh start 
```

### 创建配置集

配置集基本上是要存储的数据的蓝图。配置集存储在**服务器/Solr/配置集**

您可以创建自己的配置集，并使用它来存储您的数据。更改**托管模式**文件内容以定制配置。

*   您可以修改标签来表示要存储在一个文档中的数据字段
*   您可以定义类型，或者通过使用标记定义它来创建新类型。
*   id 字段是必填的，因此您不能删除它

在 Solr 中你可以做很多其他的事情，比如动态字段，复制字段等等。解释它们超出了本博客的范围，但是更多信息，这里有官方的[文档](https://lucene.apache.org/solr/features.html)。

现在你已经创建了一个配置并且有了 **chmod -R 777 config** 文件夹，把这个配置推给 zookeeper。

```
bin/solr zk upconfig -n config\_folder\_name -d /solr-8.0.0/server/solr/configsets/config\_folder\_name/ -z zookeeper-node:2181 
```

推送配置后，在每个 Solr 服务器上启动 SolrCloud。要安装 SolrCloud，请参考本文档[。](https://lucene.apache.org/solr/guide/6_6/getting-started-with-solrcloud.html)

### 连接到 Zookeeper

连接到动物园管理员:

```
bin/solr start -cloud -s example/cloud/node1/solr/ -c -p 8983 -h solr-node-1 -z zookeeper-node:2181 
```

Solr 将倒排索引存储在这个位置-> example/cloud/node1/solr/，所以在连接时需要提到那个路径。Zookeeper 将自动在 2 个 solr 服务器上分发碎片和副本。当你添加一些数据时，会产生一个散列，然后它会在一个特定的碎片中。这都是动物园管理员处理的。

要向服务器添加数据，您需要发布到链接 **http://:8983/solr//update？提交=真**。

IP 可以是任何服务器的，数据自动分布在各个碎片中。

要从您的 solr 搜索中获取数据**http://:8983/Solr/user/select？q=**

注意:如果你使用一个 solr 服务器作为 zookeeper，上面所有的步骤都是一样的，但是用那个 Solr 节点 ip 替换 zookeeper ip，并把端口改为 9983 而不是 2181

### 故障排除

这里有几个在设置 SolrCloud 时可能出现的常见问题。

创建 SolrCloud 并连接到 zookeeper 后，您可能会看到一个错误，如 8983 或 7574 已经在使用中。

```
:fuser -k 8983/tcp - 
```

这将找到进程并杀死它

您可能看到的另一个错误是 SolrCloud 找不到新创建的配置集。

解决方案:对新的配置集执行 chmod 777。更安全的方法是将文件夹提供给 solr 用户。

### 结论

Solr 拥有一个由经验丰富的用户和贡献者组成的大型社区，与竞争对手相比更加成熟。Solr 面临来自 Elasticsearch 的竞争，elastic search 是开源的，也是基于 Apache Lucene 构建的。Elasticsearch 被认为更擅长搜索日志数据等动态数据，而 Solr 则更好地处理静态数据。在可伸缩性方面，虽然 Elasticsearch 有更好的内置可伸缩性特性，但有了 Zookeeper 和 SolrCloud，用 Solr 也很容易伸缩。

***作者简介****:*[*Pulit Kidia*](https://www.linkedin.com/in/pulkit-kedia-389a9a122)*是一名后端工程师，拥有云服务、系统设计和创建可扩展后端系统的经验。他喜欢学习和集成新的后端技术。* [入门，免费](https://bitbucket.org/account/signup/)

*原载于 2019 年 8 月 22 日*[*【https://bitbucket.org】*](https://bitbucket.org/blog/using-apache-solr-in-production)*。*