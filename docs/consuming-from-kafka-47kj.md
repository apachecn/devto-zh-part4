# 从卡夫卡那里消费

> 原文：<https://dev.to/madhur/consuming-from-kafka-47kj>

我一直在从 Java 的卡夫卡主题中探索最佳的消费方式。有几种方法:

1 最简单的方法是使用`KafkaListener`

```
@Slf4j
@Component
public class ExampleConsumer {

    @KafkaListener(id = "fooGroup", topics = "Topic2")
    public void listen(String in) {
        log.info("Received: " + in);
        if (in.startsWith("foo")) {
            throw new RuntimeException("failed");
        }
    }
} 
```

2 第二种方式是用[阿帕奇骆驼](https://camel.apache.org/)。如果要对传入的消息应用大量过滤逻辑，并将处理后的消息输出到另一个主题或流，那么使用 Apache camel 非常有用。

```
@Component
public class CamelListener extends RouteBuilder {

    @Autowired
    private KafkaConsumerProperties kafkaConsumerProperties;

    @Override
    public void configure() throws Exception {
        from(kafkaConsumerProperties.kafkaUri()).process(exchange -> {

            String payload = exchange.getIn().getBody(String.class);
            System.out.println("Camel consumer: " + payload);
        }).end();
    }
} 
```

3 最后也是我首选的方式是使用[卡夫卡溪流](https://kafka.apache.org/documentation/streams/)

```
@Service
@Slf4j
public class KafkaStreamConsumer {

    @Autowired
    private KafkaConsumerProperties kafkaConsumerProperties;

    @Autowired
    private KafkaOrderFeedProcessor kafkaOrderFeedProcessor;

    private String topic;

    @PostConstruct
    public void processKafkaConsumer() {
        Properties properties = kafkaConsumerProperties.getConsumerProperties();
        KafkaStreams kafkaStreams = null;
        try {
            StreamsBuilder builder = new StreamsBuilder();
            KStream<String, String> kStream = builder.stream(topic);
            kStream.process(kafkaOrderFeedProcessor, new String[0]);
            kafkaStreams = new KafkaStreams(builder.build(), properties);
            kafkaStreams.start();
            log.info("op={}, status=OK, desc={}", "KafkaConsumer", "kafka consumer stream  started successfully");
        } catch (Exception var9) {
            log.error("op={}, status=KO, desc={} and exception={}", new Object[]{"KafkaConsumer", "exception while starting kafka consumer stream", var9.getMessage()});
            if (kafkaStreams != null) {
                kafkaStreams.close();
            }
        }

    }
} 
```

使用 Kafka 的 Streams API 有很多好处。

Kafka 的 Streams API([https://kafka.apache.org/documentation/streams/](https://kafka.apache.org/documentation/streams/))是建立在 Kafka 的生产者和消费者客户端之上的。它比 Kafka 消费者客户端功能更强大，表达能力也更强。以下是 Kafka Streams API 的一些特性:

*   支持恰好一次处理语义(Kafka 版本 0.11+)
*   支持容错的有状态处理，包括流式连接、聚合和窗口
*   支持事件时间处理以及基于处理时间和摄取时间的处理，对流和表都有一流的支持，这是流处理与数据库相遇的地方；实际上，大多数流处理应用程序都需要流和表来实现它们各自的用例，所以如果流处理技术缺少这两种抽象中的任何一种(比如说，不支持表),您要么被卡住，要么必须自己手动实现这个功能(祝您好运...)
*   支持交互式查询以向其他应用程序和服务公开最新的处理结果)
*   更具表现力:它配备了(1)一个函数式编程风格的 DSL，具有诸如 map、filter、reduce 等操作，以及(2)一个命令式的处理器 API，用于执行复杂事件处理(CEP)等操作，以及(3)您甚至可以将 DSL 和处理器 API 结合起来。