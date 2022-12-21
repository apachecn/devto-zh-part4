# 关于卡夫卡的几点思考

> 原文：<https://dev.to/willmenn/a-few-thoughts-about-kafka-2e2c>

在这篇文章中，我想分享一些我在和卡夫卡一起工作时遇到的陷阱。

### 重新绑扎

Kafka 有一个超时配置，也就是说，如果您无法处理提供给实例的消息集，它将重新平衡。因此，如果实例由于某种原因很慢，Kafka 会将同一个分区交给另一个实例/消费者，但是前一个分区仍在处理消息，在这种情况下，两个分区将处理相同的消息，并且这一过程将继续，直到它在所有分区中。

如何解决，首先尝试使您的流程更具性能，如果这不可能，尝试增加超时，如果这不能解决问题，Kafka API 中有这个[类](https://kafka.apache.org/0100/javadoc/org/apache/kafka/clients/consumer/ConsumerRebalanceListener.html),您可以提交当前偏移量，以使其他实例不拾取之前的消息。

### 扇出怎么做？

当我们谈论消息驱动的应用时，扇出是一种常见的模式。但是对于卡夫卡，我只知道有两种方法，我们可以在我们的系统中拥有它。

*   首先是拥有多个消费者群体，但是在这种情况下，两个群体都将收到相同的消息集。

*   第二种方法是更改 messageKey 哈希，使每个消息都不同，使 Kafka 将每个消息发送到不同的分区，这样我们就不知道 Kafka 将向每个分区发送多少消息，从而无法预测它是否会是一个循环，因为哈希函数会根据您要发送到 Kafka 以创建哈希的数据而变化。

### 如何统计题目中的消息

在 Kafka 中，我们有一个叫做分区偏移量和提交偏移量的概念，我们没有主题中的消息数量。最后，您只有提交的偏移量和分区偏移量之间的延迟。

`$ LAG = (partitionOffset - commitedOffset) by partition by topic`

在 Java 中使用 Kafka API:

```
 public List<OffsetAgg> getLAG() {
ListConsumerGroupOffsetsResult listOffsets = adminClient.listConsumerGroupOffsets("consumer-group");
        return listOffsets.partitionsToOffsetAndMetadata()
                .get()
                .entrySet()
                .stream()
                .map((entry) -> {
                    return OffsetAgg.builder()
                            .partition(entry.getKey().partition())
                            .topicName(entry.getKey().topic())
                            .committedOffset(entry.getValue().offset())
                            .build();
                }).collect(groupingBy(OffsetAgg::getTopicName))
                .entrySet()
                .stream()
                .flatMap(entry -> getPartitionsOffset(env, entry.getValue()).stream())
                .collect(Collectors.toList());

    }

    public List<OffsetAgg> getPartitionsOffset(Env env, List<OffsetAgg> offsetAggs) {
        KafkaConsumer consumer = consumerMap.get(env);
        List<TopicPartition> topicPartitions = offsetAggs
                .stream()
                .map(agg -> buildTopicPartition(agg))
                .collect(Collectors.toList());
        Map<TopicPartition, Long> endOffsets = consumer.endOffsets(topicPartitions);

        return offsetAggs
                .stream()
                .map(agg -> agg.toBuilder().partitionOffset(endOffsets.get(buildTopicPartition(agg))).build())
                .collect(Collectors.toList());
    }

    private TopicPartition buildTopicPartition(OffsetAgg agg) {
        return new TopicPartition(agg.getTopicName(), agg.getPartition());
    }

    @AllArgsConstructor
    @Getter
    @Builder(toBuilder = true)
    public static class OffsetAgg {
        private Long partitionOffset;
        private Long committedOffset;
        private Integer partition;
        private String topicName;
    } 
```

我写这篇文章是为了帮助开发者在使用 Kafka 时理解一些事情。
重新平衡很好，但是您需要更改使用预取消息的默认时间戳来满足您的需求，或者在 Kafka 中添加一个配置，以便在将分区分配给另一个实例之前提交偏移量。
Fanout 是一种常见的实现模式，但要知道在 Kafka 中实现它并不容易，为了实现这一点，您需要利用排序机制或在分区中添加消费者组。
我习惯于 RabbitMq，所以我通常会查看消息的数量，所以在 Kafka 中，因为它有分区偏移和消费者偏移，所以在代码中，要获得分区中有多少消息的实际数量有点棘手，我能够理解偏移，并从中得出 Kafka 和每个消费者中的消息数量。