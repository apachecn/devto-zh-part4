# 卡夫卡连线-速成班

> 原文：<https://dev.to/thegroo/kafka-connect-crash-course-1chd>

[Kafka Connect](https://kafka.apache.org/documentation/#connectapi) 正在成为[改变数据捕捉](https://en.wikipedia.org/wiki/Change_data_capture)领域的一股力量。

Kafka 本身已经获得了很大的发展势头，被越来越多的公司采用，这些公司试图将其数据工作负载从批处理转移到事件的微批处理/实时处理，以及使用它的其他实际可行的解决方案。

Kafka Connect 是一个工具，它有助于在一个组织内的不同筒仓之间实现几乎实时的数据同步。以其清晰的方法连接系统，帮助您移动数据并对数据进行简单的转换。

Kafka Connect 使我们能够在依赖标准 Kafka 功能(如可扩展性、容错性和高可用性)的同时，解决当今企业内部成百上千个不同系统所需的“实时”数据集成。

来自 Kafka 文档:

> Kafka Connect 是一个工具，用于在 Apache Kafka 和其他系统之间可扩展和可靠地传输数据。它使得快速定义将大量数据移入和移出 Kafka 的连接器变得简单。Kafka Connect 可以接收整个数据库或从所有应用服务器收集指标到 Kafka 主题中，使数据可用于低延迟的流处理。

### 连接器的类型

**源连接器**:从另一个系统导入数据到 Kafka

**接收器连接器**:将数据从 Kafka 导出到另一个系统

### 卡夫卡连接器建筑概述

[![Kafka Connect Overview](img/9ebec91b6e50a4bf2c349f4843557220.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lgPmmjPW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vo541hjz1e482216bch0.png)

### 执行方式

Kafka Connect 目前支持两种执行模式:独立(单个进程)和分布式。

在独立模式下，所有工作都在单个进程中执行，这更容易开始，并且在只有一个工作人员有意义的情况下可能有用(例如，收集日志文件)，但它不会受益于 Kafka Connect 的一些功能，例如容错。

分布式模式自动处理工作的平衡，允许您动态地扩大(或缩小)规模，并在活动任务以及配置和偏移提交数据中提供容错。

在写这篇文章时，Kafka Connect 2.3.0 在其最新版本中加入了一个新的实现功能，这使它更具吸引力，它现在只能在经历重新平衡时部分暂停处理消息。一个特性叫做，**增量协作再平衡**。
也就是说，在重新平衡过程中，只有由一个工作线程处理的受影响的分区会被暂停，而连接到不同分区的其他工作线程可以继续处理。

我注意到的一件事是 Kafka Connect 可用的教程和说明无处不在，但它们大多涵盖了合流连接器和其他一些现有平台，并建议使用它们的 CLI 来安装和配置连接器，这很好，并在企业环境中为运行生产工作负载带来了许多好处，但这些工具对我们这样的开发人员隐藏了连接器实现及其背后的可能性。 因此，本文的目标是涵盖基础知识，并使用“普通的”Kafka 发行版和命令行运行尽可能简单的 Kafka Connect，没有特定的供应商在它上面添加插件。

## 设置

*   克隆回购

```
git clone git@github.com:stockgeeks/docker-compose.git 
```

Enter fullscreen mode Exit fullscreen mode

为了运行这篇文章中的例子，我们将使用一个 docker-compose 文件和我们所有的依赖项来运行 Kafka 和一个配置了内置 FileStream 源连接器的额外容器。

有关 Kafka 和 Zookeeper 的 docker-compose 设置的详细信息，请查看本文[之前的文章](https://dev.to/thegroo/one-to-run-them-all-1mg6)，使用一些有用的命令和技巧为本地开发运行一个基本的本地 Kafka 实例。

我们将使用 [wurstmeister/kafka](https://hub.docker.com/r/wurstmeister/kafka/) 图像，因为它是直接来自[开源 kafka 发行版](https://github.com/apache/kafka)的纯构建。

完整的 docker-compose 文件可以在 kafka-connect-crash-course 文件夹下克隆这个 repo 来获得。这里有一个[到它的直接链接](https://github.com/stockgeeks/docker-compose/blob/master/kafka-connect-crash-course/docker-compose.yml)。

#### 撰写声明的服务解释

撰写文件设置了 3 个容器，前两个容器从 docker hub 中提取，并使用 Kafka 和 Zookeeper 的 [wurstmeister 图像。](https://hub.docker.com/u/wurstmeister) 

```
 # https://github.com/wurstmeister/zookeeper-docker
  zookeeper:
    container_name: zookeeper
    image: wurstmeister/zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
    ports:
      - '2181:2181'

  # https://hub.docker.com/r/wurstmeister/kafka/
  kafka:
    container_name: kafka
    image: wurstmeister/kafka:2.12-2.3.0
    environment:
      ## the >- used below infers a value which is a string and properly
      ## ignore the multiple lines resulting in one long string:
      ## https://yaml.org/spec/1.2/spec.html

      ## You need to make sure to specify your hostname in a file in this
      ## same dir as this compose file called `.env`(uncomment the line)
      ## or to register in your `/etc/hosts` kafka as your loopback interface
      ## address together with hostname and 127.0.0.1
      KAFKA_ADVERTISED_LISTENERS: >-
        LISTENER_DOCKER_INTERNAL://kafka:19092,
        LISTENER_DOCKER_EXTERNAL://${DOCKER_HOST_IP:-kafka}:9092

      KAFKA_LISTENERS: >-
        LISTENER_DOCKER_INTERNAL://:19092,
        LISTENER_DOCKER_EXTERNAL://:9092

      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: >-
        LISTENER_DOCKER_INTERNAL:PLAINTEXT,
        LISTENER_DOCKER_EXTERNAL:PLAINTEXT

      KAFKA_INTER_BROKER_LISTENER_NAME: LISTENER_DOCKER_INTERNAL
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      # we create topic with 1 partition and 1 replica as it's for local dev and we're running a single broker instance.
      KAFKA_CREATE_TOPICS: 'simple-connect:1:1'
      KAFKA_LOG4J_LOGGERS: >-
        kafka.controller=INFO,
        kafka.producer.async.DefaultEventHandler=INFO,
        state.change.logger=INFO

    ports:
      - 9092:9092
    depends_on:
      - zookeeper
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock 
```

Enter fullscreen mode Exit fullscreen mode

第三个是连接器，它内置于项目中，展示了如何为本地开发配置和设置 Kafka Connect。它依靠 [docker-compose 能力从 docker 文件](https://docs.docker.com/compose/compose-file/#build)构建 docker 映像。

```
connect-standalone:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: connect-standalone
    ports:
      - 8083:8083
    depends_on:
      - kafka
    volumes:
      - /tmp:/tmp 
```

Enter fullscreen mode Exit fullscreen mode

所以我们正在从一个[docker 文件](https://docs.docker.com/engine/reference/builder/)构建图像，让我们来看看:

```
# we start from a Kafka image as the connector is in Kafka distribution
FROM wurstmeister/kafka:2.12-2.3.0

# we replace the default connect-standalone.properties so we can properly resolve to our local Kafka docker development
COPY connect-standalone.properties /opt/kafka/config/

COPY connect-file-source.properties /opt/kafka/config/

# we replace the start command creating a connector instead of starting a kafka broker.
COPY start-kafka.sh /usr/bin/

# permissions
RUN chmod a+x /usr/bin/start-kafka.sh 
```

Enter fullscreen mode Exit fullscreen mode

start-kafka.sh 脚本是一个非常简单的脚本，它使用提供的连接器脚本来初始化一个独立的连接器，将它的通用属性和特定的连接器属性文件作为参数:

```
# connector start command here.
exec "/opt/kafka/bin/connect-standalone.sh" "/opt/kafka/config/connect-standalone.properties" "/opt/kafka/config/connect-file-source.properties" 
```

Enter fullscreen mode Exit fullscreen mode

我们覆盖了一般的`connect-standalone.properties`来改变在 docker-compose 设置中运行的 kafka 代理的解析名称，因此我们已经在 docker 网络中把引导服务器的行改变为可解析的名称【T1:

```
bootstrap.servers=kafka:9092 
```

Enter fullscreen mode Exit fullscreen mode

而`connect-file-source.properties`是内置 FileStreamSource 连接器的本地配置。这个连接器是作为 Apache Kafka 发行版的一部分提供的。文件的内容在不能自我解释时会被注释。

```
name=file-source-connector
connector.class=FileStreamSource
tasks.max=1

# the file from where the connector should read lines and publish to kafka, this is inside the docker container so we have this
# mount in the compose file mapping this to an external file where we have rights to read and write and use that as input. file=/tmp/my-source-file.txt

# data read from the file will be published to the specified topic topic=simple-connect

# We override the defaults for this connector example as we want to quickly just validate it for now. key.converter=org.apache.kafka.connect.storage.StringConverter
value.converter=org.apache.kafka.connect.storage.StringConverter

# We don't need converters for the simple example key.converter.schemas.enable=false
value.converter.schemas.enable=false 
```

Enter fullscreen mode Exit fullscreen mode

为简单起见，我们覆盖了键和值转换器和模式，并将用作输入的文件配置为`/tmp/my-source-file.txt`。这是一个内部容器路径，然后我们将它映射到工作区中的一个外部卷，我们知道在那里我们拥有读写权限，fromi docker-compose.yml

```
connect-standalone:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: connect-standalone
    ports:
      - 8083:8083
    depends_on:
      - kafka
    volumes:
      - /tmp:/tmp 
```

Enter fullscreen mode Exit fullscreen mode

## 奔跑吧

*   在本地构建自定义 docker 映像，导航到 docker-compose 所在的目录并运行:`docker-compose build`。这将下载所需的映像并构建连接器映像。

*   运行它:`docker-compose up -d`将在后台运行容器，然后你可以用`docker ps`或`docker-compose ps`检查正在运行的容器，应该有 3 个容器在运行:

[![connect-setup-running](img/7d4f3654a8a53d1f0e1e44dae8853c5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MHMwu1DH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d54sj0t2r2x12io0vbsq.png)

*   现在，让我们将一个控制台消费者附加到主题，这样我们就可以在新消息发布到它时进行消费，输入 Kafka 运行容器:

```
docker exec -it kafka /opt/kafka/bin/kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic simple-connect --from-beginning 
```

Enter fullscreen mode Exit fullscreen mode

> 有一些很好的工具可以让您以更简单的方式运行 Kafka 命令(参见本文末尾的参考资料)，但它们通常包含基本的命令和脚本，我发现出于学习目的了解核心可用命令很重要，这就是为什么我不建议您最初使用这些工具。

#### 一些有用的休息电话

Kafka Connectors 框架默认公开了一个 REST 端口，因此我们可以获得一些有用的信息并发出一些命令来管理它。你可以用你喜欢的工具与它互动，我主要用的是[卷毛](https://curl.haxx.se/docs/)、[失眠](https://support.insomnia.rest/)和[邮差](https://learning.getpostman.com/docs/postman/api_documentation/intro_to_api_documentation/)。

请看一些查询信息的例子:

```
# returns a list of active connectors
GET /connectors

# information about the specified connector
GET /connectors/{name}

# configuration for specified connector
GET /connectors/{name}/config

# status of specified connector
GET /connectors/{name}/status

# connector tasks
GET /connectors/{name}/tasks

# status of tasks for specified connector
GET /connectors/{name}/tasks/{taskid}/status

# list of connector installed plugins
GET /connector-plugins 
```

Enter fullscreen mode Exit fullscreen mode

# 参考文献

[Apache Kafka 2.3.0 发行说明](https://www.apache.org/dist/kafka/2.3.0/RELEASE_NOTES.html)

[Apache Kafka Connect 文档](https://kafka.apache.org/documentation/#connect)

[汇合连接器开发者文档](https://docs.confluent.io/current/connect/devguide.html)

[汇流连接器集线器](https://www.confluent.io/hub)

其他连接器- [Debezium](https://debezium.io/) 、 [Landoop](https://lenses.io/connect/) 以及许多其他的连接器，只要用你最喜欢的搜索引擎搜索一下就能找到。

一些卡夫卡工具- [汇流 CLI](https://docs.confluent.io/current/cloud/cli/index.html) ， [KafkaCat](https://github.com/edenhill/kafkacat)