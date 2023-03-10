# 高通量 kafka 生产者的考虑

> 原文：<https://dev.to/madhur/considerations-for-high-throughput-kafka-producer-3gjm>

我最近一直在研究大量高产量的卡夫卡作品。我们的应用程序每天发布近 300 万条 kafka 发布的内容。(与卡夫卡能处理的相比，这仍然很低)

在维持这样的卡夫卡生产者的过程中，有一些经验教训:

*   明智地选择分区的数量:分区的数量决定了消费者可以伸缩的范围。在卡夫卡中，分区的数量是平行度。Kafka 将单个分区的数据交给单线程。

我们一般的经验法则是分区数量等于消费者服务器的数量。例如，如果我们有一个由 20 台服务器组成的集群在使用 kafka 主题，那么每台服务器都将使用一个分区，也就是 20 个分区。

还有许多其他因素需要考虑[，如这里所解释的](https://www.confluent.io/blog/how-choose-number-topics-partitions-kafka-cluster)

*   发布时确定一致的密钥——使用相同密钥发布的消息将被发布到单个分区。分区是消息排序的逻辑单元。因此，如果消息的排序对您很重要，您应该为这些消息选择一致的键。

*   除非您显式使用阻塞调用，否则默认情况下 Kafka producer 是异步的。这意味着 kafka publish 可能会失败，您的代码可能已经通过了 publish 方法。一旦服务器执行了发布指令，Kafka producer 就会提供回调。在这个回调中，用户可以检查失败并重试选项或发送到死信队列等。Kafka producer 本身会重试 3 次，但我认为这对于数据关键型应用程序来说太少，也不够。

下面是这样的生产者
的样本片段

```
@Autowired
@Qualifier("createKafkaSslProducerOrder")
Producer kafkaSslProducer;

public void publish(String messageKey, String payload, String topic) {

    try {

        ProducerRecord record = new ProducerRecord<>(topic, messageKey, payload);

        kafkaSslProducer.send(record, (metadata, exception) -> {

            if (Optional.ofNullable(exception).isPresent()) {
                log.error("op={}, status=KO, desc={} and exception={}",
                        new Object[] { "KafkaProducer",
                                "Error posting message to kafka topic: " + topic,
                                exception.getMessage() });
                // Send for re-processing
            }

        });

    } catch (Exception ex) {
        log.error("op={}, status=KO, desc=Error posting message to SSL kafka: {}, stackTrace={} ", LOG_OP_INFO, ex.getMessage(), ex);
        // Re-throw the exception so that status can be recorded in the database.
    }
} 
```

*   在 kafka 消息的情况下，提供消息的完整发布时间戳和原始修改时间戳(例如 db 记录)是有用的。使用这些时间戳，客户端可以确定传入的消息是旧的还是新的。

*   最初，在开发过程中，存储所使用消息的分区和偏移量非常有用。这可以存储在消费者数据存储或应用程序日志中。利用这些信息，可以在 kafka 中直接查找消息以查看原始消息。