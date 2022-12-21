# 今天的三件事- 8 月 30 日

> 原文：<https://dev.to/goatmale/three-things-from-today-8-30-57ok>

# 2019 年 8 月 30 日

### 周五快乐！

我吃得有点多了🍷昨天所以我有点迷糊，但是这并不妨碍我今天学习！

## 1。火花 S3 调整

Spark 似乎使用了 parqueoutputcommitter——Hadoop 的一个功能，将拼花文件写入 S3。

深入研究我们在向 S3 写 Spark 时遇到的问题，我们[遇到了这里描述的修复方法](http://agrajmangal.in/blog/big-data/spark-parquet-s3/)，它涉及到在 hadoop 中设置一个配置值:

测试它-它似乎工作。

我做了一个 PR 来设置从 Hadoop / Hive 方面向前发展。

```
 hadoop_conf.set('spark.hadoop.mapreduce.fileoutputcommitter.algorithm.version', '2') 
```

Enter fullscreen mode Exit fullscreen mode

这也可以在 Spark 中用以下属性设置:

```
'spark.hadoop.mapreduce.fileoutputcommitter.algorithm.version':2 
```

Enter fullscreen mode Exit fullscreen mode

## 2。头盔 3 beta 2 发布

办公室里的传言是头盔 3 测试版 2 已经发布。头盔 3 是头盔的一个重要版本，因为它消除了对舵杆的依赖。这是如此重要，以至于他们似乎有理由在他们的网站[上发布一个 7 集的博客系列](https://helm.sh/blog/helm-3-preview-pt1/)

此外，微软有一篇很棒的文章描述了为什么 Helm 3 很重要。

## 3。什么是阿帕奇齐柏林飞艇？和 Jupyter 有什么不同？

Apache Zeppelin 和 Jupyter 都是交互式笔记本，可以用来做数据科学方面的事情，如执行计算、绘制图表等。

Jupyter 笔记本在后台运行 python。Apache Zeppelin 在幕后使用 JVM。

至于我喜欢的特性，Jupyter 是 iPython 的一个分支，我很喜欢它做 Python 的工作。对于非 python 语言来说，Apache Zeppelin 似乎更健壮一点，而且他们的演示也很棒，能够使用他们可爱的 Angular 图形用户界面真是太棒了。

请看这些图表！

## 4。奖金的事情！！

很久以来，我一直对自己承诺要学习普罗米修斯。是时候深入了解一下[令人敬畏的普罗米修斯](https://github.com/roaldnefs/awesome-prometheus)名单了....

我还在自己的谷歌云账户上创建了一个新的 GKE 集群进行测试..与 Azure 和 AWS 相比，这是 Kubernetes 的简单模式。下周还会有更多关于监控和我的新测试集群的内容:)

劳动节快乐-周二见！