# Docker 中的本地 DynamoDB

> 原文：<https://dev.to/harshadranganathan/dynamodb-local-in-docker-2odj>

## 简介

DynamoDB 本地 docker 映像使您能够通过使用内置了所有 DynamoDB 本地依赖项和必要配置的 Docker 映像，快速开始使用 DynamoDB 本地。新的 Docker 映像还使您能够将 DynamoDB local 包含在您的容器化构建中，并作为您的持续集成测试的一部分。

图片可在:[https://hub.docker.com/r/amazon/dynamodb-local](https://hub.docker.com/r/amazon/dynamodb-local)获得

## 复合坞站

我们将创建一个 docker 合成文件来启动 dynamo db local 作为一个容器，其他容器可以访问它。

```
version: '2'
services:
  dynamodb:
    image: amazon/dynamodb-local:latest
    ports:
      - "8000:8000"
    command: ["-jar", "DynamoDBLocal.jar", "-sharedDb", "-inMemory"] 
```

这里，

图像-

我们拉亚马逊/dynamodb 的最新版本-本地图像

端口-

将容器端口 8000 暴露给本地端口 8000，因为本地 dynamo db 在该端口上运行

命令

默认情况下，本地 dynamo db 以 inMemory 设置启动。我们在这里用一个额外的参数-- shared db 覆盖了这个命令。这是为了确保 dynamo db 对每个凭证和区域使用单个数据库文件，而不是单独的文件。如果您没有启用此设置，那么如果您的应用程序容器和本地 CLI 使用不同的 AWS 信用，那么它们将不会访问相同的 dynamo db 状态。通过 CLI 创建的表对应用程序容器不可见。

运行 docker compose，并通过使用 CLI 命令中的端点 url 选项来访问本地 dynamo db。

```
docker-compose up

aws dynamodb list-tables --endpoint-url http://localhost:8000 
```

如果你没有使用默认的网络模式，而是使用桥接网络模式，这就是你的 docker 撰写看起来的样子:

```
version: '2'
services:
  dynamodb:
    image: amazon/dynamodb-local:latest
    ports:
      - "8000:8000"
    command: ["-jar", "DynamoDBLocal.jar", "-sharedDb", "-inMemory"]
    networks:
      test-network:
        ipv4_address: 172.16.231.1
networks:
  test-network:
    driver: bridge
    driver_opts:
      com.docker.network.enable_ipv6: "false"
    ipam:
      driver: default
      config:
        - subnet: 172.16.231.0/24
          gateway: 172.16.231.1 
```

## 从另一个容器访问 DynamoDB 本地容器

假设您有一个用 Java 编写的运行在 tomcat 容器中的 webapp，并且您想要访问本地 dynamo db 容器来进行集成测试。

您可以使用与容器名称相同的主机名从 webapp 容器访问本地 dynamodb，在这种情况下，端点 url 将是 [http://dynamodb:8000](http://dynamodb:8000) 。

在您的 webapp 中，当您想要访问本地 dynamo db 容器时，通过将端点 url 设置为[http://dynamo db:8000:](http://dynamodb:8000:)
来构造客户端

```
public AmazonDynamoDB dynamoDbClient(final String region) {
  return AmazonDynamoDBClientBuilder.standard()
      .withClientConfiguration(defaultClientConfiguration)
      .withCredentials(new DefaultAWSCredentialsProviderChain)
      .withRegion(region)
      .build();
}

/**
 * Returns Dynamo DB client to access local copy
 * where serviceEndpoint is http://dynamodb:8000 and region can be acceptable region names e.g. us-east-1
*/
public AmazonDynamoDB dynamoDbClient(final String region, final String serviceEndpoint) {
  return AmazonDynamoDBClientBuilder.standard()
      .withClientConfiguration(defaultClientConfiguration)
      .withCredentials(new DefaultAWSCredentialsProviderChain)
      .withRegion(region)
      .withEndpointConfiguration(new EndpointConfiguration(serviceEndpoint, region))
      .build();
} 
```

然后在 docker 合成文件中，需要为 webapp 容器设置 AWS creds 和 region。

```
version: '2'
services:
  dynamodb:
    image: amazon/dynamodb-local:latest
    ports:
      - "8000:8000"
    command: ["-jar", "DynamoDBLocal.jar", "-sharedDb", "-inMemory"]
  tomcat:
    image: webapp:latest
    environment:
      - AWS_ACCESS_KEY_ID=dummy
      - AWS_SECRET_ACCESS_KEY=dummy
      - AWS_REGION=us-east-1
    ports:
      - "80:8080" 
```

这里，

图像-

tomcat 中运行的 web 应用程序的 Docker 图像

环境-

将 AWS _ ACCESS _ KEY _ ID & AWS _ SECRET _ ACCESS _ KEY 设置为任何虚拟值，否则身份证明链将失败，并指出它无法检测任何身份证明。

## 创建表格

在您的应用程序可以向本地 dynamodb 读取/写入任何数据之前，您必须创建所需的表。

您可以创建表格作为 webapp 代码的一部分(或者您可以将一个脚本捆绑到您的 web app 映像，该脚本将使用 AWS CLI 创建表格并启动您的应用程序。

您的 webapp docker 构建文件可以如下所示:

```
FROM openjdk:8-jre-alpine

ENV TOMCAT_VERSION 8.0.36
RUN \
    wget https://archive.apache.org/dist/tomcat/tomcat-8/v${TOMCAT_VERSION}/bin/apache-tomcat-${TOMCAT_VERSION}.tar.gz && \
    tar xvzf apache-tomcat-${TOMCAT_VERSION}.tar.gz && \
    mv apache-tomcat-${TOMCAT_VERSION} tomcat && \
    rm -f apache-tomcat-${TOMCAT_VERSION}.tar.gz

RUN pip install awscli --upgrade --user
ENV PATH="$PATH:/root/.local/bin"

COPY start.sh .

ENV JPDA_ADDRESS 8000
ENV JPDA_TRANSPORT dt_socket
ENV JDPA_OPTS -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=8000

EXPOSE 8080 8000 8009

ENTRYPOINT ["start.sh"] 
```

在启动脚本中，创建表并运行 catalina。

```
#!/usr/bin/env bash
aws dynamodb create-table --table-name <table_name> --attribute-definitions <attribute_definitions> \
--key-schema <key_schema> --billing-mode <billing_mode> --endpoint-url http://dynamodb:8000 --region <region>

./tomcat/bin/catalina.sh jpda run 
```