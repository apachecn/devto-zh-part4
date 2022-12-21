# 并行和排序

> 原文：<https://dev.to/madhur/parallelism-and-ordering-3fa7>

基于微服务事件的架构的一个缺点是，跨多个模块对单个实体进行大量并行处理。在很多情况下，我们希望并行，但同时又希望消息以有序的方式处理。

有几种设计模式可以解决这个问题:

*   卡夫卡通过[分区](http://kafka.apache.org/090/documentation.html)解决了这个问题。一个主题可以分成几个部分。确保消息在单个分区内有序。您可以让多个消费者分别从每个分区消费，从而提供可伸缩性和排序。这种设计假设每个消费者都是单线程的，并且一个接一个地处理消息。实际上，情况通常不是这样。

*   解决方案之一是使用 SEDA 队列。 [Camel](https://camel.apache.org/) 有一个关于[并行和排序的专门页面](https://camel.apache.org/parallel-processing-and-ordering.html)

*   有一份白皮书标题为[有序并行的可扩展架构](https://people.csail.mit.edu/sanchez/papers/2015.swarm.micro.pdf)
    我还没有看完。

在并行处理消息时，是否有其他方法来处理排序？请在评论中告诉我...