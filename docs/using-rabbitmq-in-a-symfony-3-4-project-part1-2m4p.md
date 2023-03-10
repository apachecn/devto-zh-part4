# 在 Symfony 3.4 项目中使用 RabbitMQ 第 1 部分:生产者-

> 原文：<https://dev.to/haamida/using-rabbitmq-in-a-symfony-3-4-project-part1-2m4p>

当我不得不在 symfony 项目中使用 RabbitMQ 时，我很难选择使用 bundle 还是 Component，所以在这个例子中，我使用 Enqueue 和 symfony 3.4 创建了一个完整的 fonctionnal 例子。按照 Enqueue 的选择，我发现它([enqueue.forma-pro.com](https://enqueue.forma-pro.com/))是 php 项目最好的文档化解决方案，并配有各种消息代理(amqp，redis，kafka，...).

这个例子将允许我们以一种随机的方式为 r/SpaceX API 连接到不同的端点。所有代码示例和配置都严格适用于**队列捆绑包**的 **0.9.12** 版本。完整的代码可以在[https://github.com/Haamida/RestRabbit](https://github.com/Haamida/RestRabbit)上找到。

#### 1。配置

第一步是运行

```
composer require enqueue/enqueue-bundle enqueue/amqp-lib 
```

这里是我选择连接到 RabbitMQ 的库。我应该在这里提到，amqp-lib 依赖于 bcmath 扩展，所以你必须确保它已经安装或启用。

既然包已经添加到项目中(composer.json 和 AppKernel.php)，让我们继续在 enqueue.yml :
中进行最小化配置

```
enqueue:
  default:
    transport: 'amqp+lib:'
    client: ~ 
```

现在 parameters.yml 中的连接参数

```
 queue_rb: spacex_q
    topic_rb: spacex_t
    host_rb: 127.0.0.1
    port_rb: 5672
    user_rb: guest
    pass_rb: guest
    vhost_rb: / 
```

这里为了简单起见，我保留了默认端口 user、vhost，...(当然应该为生产而改变)。
启用兔子管理插件来监控与队列的交互将会很有用:
`rabbitmq-plugins enable rabbitmq_management`
现在前往`http://localhost:15672/`以获得队列和消息的概览。

#### 2。连接、主题和队列

要将生成的消息发送到队列，我们需要建立连接。
第一步是获取连接工厂的实例:

```
$factory = new AmqpConnectionFactory([
            'host' => $this->getParameter('host_rb'),
            'port' => $this->getParameter('port_rb'),
            'vhost' => $this->getParameter('vhost_rb'),
            'user' => $this->getParameter('user_rb'),
            'pass' => $this->getParameter('pass_rb'),
            'persisted' => true,
        ]); 
```

这将允许我们现在创建一个**连接上下文**一个主题和一个队列(如果它们还没有被创建)

```
 $context = $factory->createContext();
 $topic = $context->createTopic($topicName);
 $topic->setType(AmqpTopic::TYPE_DIRECT);
 $context->declareTopic($topic);
 $queue=$this->createOrUseQueue($context,$queueName);
 $context->bind(new AmqpBind($topic, $queue)); 
```

在上面的代码中，我创建了直接交换类型的主题，这样消息将被直接路由到具有匹配路由关键字的队列。这是因为根据 rabbitmq 消息流，消息首先被传递到交换，而不是取决于交换类型:

*   **Direct** :直接发送到相应的(通过路由键)队列
*   **Fanout** :发送给所有绑定到交换机的队列
*   **头**:根据消息头发送
*   **主题**:根据路由关键字和路由模式之间的对应关系发送到一个或多个队列，这些是主要的交换类型，在这里可以找到更深入的解释[交换-路由关键字-绑定](https://www.cloudamqp.com/blog/2015-09-03-part4-rabbitmq-for-beginners-exchanges-routing-keys-bindings.html)。配置完主题后，我们继续创建队列，稍后我们将绑定到该队列。

```
 public function createOrUseQueue($context,$queueName){
        $queue = $context->createQueue($queueName);
        $queue->addFlag(AmqpQueue::FLAG_DURABLE);
        $context->declareQueue($queue);
        return $queue;
    } 
```

实际上，创建主题的逻辑是一样的。我添加了标志`FLAG_DURABLE`,这样队列可以在代理重启/技术问题中存活。也就是说，持久队列并不保证消息也能存活，它们应该被标记为 persistant 以达到这种效果。

#### 发送消息到队列

现在一切都设置好了，我们可以生成和发送消息了:

```
 switch (rand(1, 3)) {
                case 1:
                    $message = $context->createMessage('roadster');
                    break;
                case 2:
                    $message = $context->createMessage('rockets');
                    break;
                case 3:
                    $message = $context->createMessage('missions');
                    break;
                default:
                    $message = $context->createMessage('info');
            }
$context->createProducer()->send($this->createOrUseQueue($context, $this->getParameter('queue_rb')), $message); 
```

这是生产部分的全部内容，在下一部分中，我们将把我们的消息配置为持久的，并创建允许我们从 SpaceX API 获取信息的消费者。