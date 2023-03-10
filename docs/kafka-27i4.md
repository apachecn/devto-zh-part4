# 卡夫卡

> 原文：<https://dev.to/vikashagrawal/kafka-27i4>

卡夫卡有 4 个组成部分:

```
o Producer
o Consumer
o Broker: A broker is nothing but the server and a node, which manages the topic.
o Zookeeper: Zookeeper maintains the state of brokers, in case of any broker going down or coming up, it notifies the zookeeper and zookeeper informs to the producer and consumer so that producer and consumer are connected to live broker. 
```

Enter fullscreen mode Exit fullscreen mode

主题是消息类型的分类，比如说数据类型。给定的主题被分割成许多部分。
如果代理的数量超过 1 个，则这些分区在超过 1 个代理之间复制。
对于给定的分区，将只有 1 个主节点，其余的将是从节点。
来自生产者的消息可以进入任何分区，因此得到复制。
如果消息被分配了密钥，那么它确保消息在特定分区中具有相同的密钥。
通过这种方式，它确保消息在分区中是有序的。
·Kafka 客户端(生产者/消费者)到 Kafka 的数据传输通过 https 进行，https 确保使用 SSL 证书对数据进行加密。
使用案例:

```
o Messaging: A messaging platform built in publisher-subscriber model.
o Website tracking: to get the real time report of user activity in the website.
o Log aggregation: To aggregate the logs from many services.
o Fault tolerance: Kafka platform enables the back up of the events along with regular synch up.
o Stream Processing: events coming from multiple services can be processed in real 
time and then can be transferred to other platform like storm for further processing. 
```

Enter fullscreen mode Exit fullscreen mode

伊姆斯:t0]

```
o Twitter uses it to take the post from the registered users to another registered 
user to repost and read and to unregistered user for read.
o LinkedIn uses it for online consumption along with offline analytics while being 
sorted in Hadoop.
o Netflix uses it for real time monitoring and event processing. 
```

Enter fullscreen mode Exit fullscreen mode