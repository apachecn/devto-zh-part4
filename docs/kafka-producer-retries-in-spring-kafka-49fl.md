# 卡夫卡生产者重试春天卡夫卡

> 原文：<https://dev.to/nagarajan/kafka-producer-retries-in-spring-kafka-49fl>

KafkaProducer 配置有一个选项( *retries* 属性)，用于在出现可重试异常(如 NoLeaderForPartition、NetworkException、TimeoutException 等)时进行自动重试。

**重试次数**属性的默认值为**整数。根据 https://kafka.apache.org/documentation/#producerconfigs 的*T5*最大**。

我在应用程序中使用了*spring-kafka 2 . 2 . 8-RELEASE*,并试图通过从 Kafka 集群中取出一个代理来测试重试行为。当一个分区的领导者代理关闭时，它抛出了 NoLeaderForPartition 异常，并且需要 1-2 秒的时间将一个新的领导者分配给该分区。

根据文档，生产者应该重试这个异常，但令人惊讶的是它没有发生。进一步挖掘，发现 spring-Kafka 2 . 2 . 8-RELEASE(2019 年 8 月，中央回购的最新版本)使用的是 2.0.1 版本的 *kafka-clients* 库，而 kafka-clients 库的最新版本是 2.3.0。在 kafka-clients 2.0.1 版本中，重试次数的默认值为 0。

我从中学到的是，*最新的 spring 库版本不需要使用实际客户端库的最新版本*。我们还需要了解文档适用于哪个版本的客户端库。