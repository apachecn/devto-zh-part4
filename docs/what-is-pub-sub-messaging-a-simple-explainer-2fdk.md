# 什么是发布/订阅消息？一个简单的解释者。

> 原文：<https://dev.to/gibbiv/what-is-pub-sub-messaging-a-simple-explainer-2fdk>

## 定义

发布/订阅是发布/订阅消息传递的简写，这是一种异步通信方法，在应用程序之间交换消息时不知道发送者或接收者的身份。

## 概述

发布/订阅模式由四个核心概念组成:

*   **主题**–维护订阅者列表的中间渠道，用于转发从发布者处接收的消息
*   **消息**–发布者发送给主题的序列化消息，发布者对订阅者一无所知
*   **发布者**–向主题发布消息的应用程序
*   **订阅者**–一个应用程序，它向所需的主题注册，以便接收适当的消息

### 发布/订阅的优缺点

与所有技术一样，使用发布/订阅消息传递有优点也有缺点。两个主要优势是松耦合和可伸缩性。

**松耦合**

发布者永远不会意识到订阅者的存在，所以两个系统可以彼此独立运行。这种方法消除了传统耦合中存在的服务依赖性。例如，如果服务器进程没有运行，客户端通常无法向服务器发送消息。通过发布/订阅，客户端不再关心服务器上是否正在运行进程。

**可扩展性**

发布/订阅消息可以扩展到超出单个传统数据中心能力的数量。这种级别的可伸缩性主要是由于并行操作、消息缓存、基于树的路由以及内置于发布/订阅模型中的其他多种功能。

[虽然](https://blog.kuzzle.io/pub-sub-for-real-time-applications)可扩展性确实有限制。增加节点和消息的数量也会增加负载激增或降低的几率。除此之外，发布/订阅模式的优势有时会被它遇到的消息传递问题所掩盖，例如:

*   发布者可以仅在特定时间段内传递消息，而不管消息是否被接收到。
*   由于发布者没有进入订阅者的窗口，它将总是假定适当的订阅者正在侦听。如果订阅者没有在听，错过了重要的消息，对生产系统来说可能是灾难性的。

## 发布/订阅如何工作

在概述中，我们介绍了发布者如何向主题发送消息，以及主题如何将消息转发给适当的订阅者。从拓扑的角度来看，这是一个简单的过程。

当涉及到对发布或订阅过程进行编码时，该模型可能会有点混乱。考虑以下用于创建主题的 Java 代码。

```
Topic createTopic(String topicName) throws IOException {
  String topic = getTopic(topicName); // adds project name and resource type
  Pubsub.Projects.Topics topics = pubsub.projects().topics();
  ListTopicsResponse list = topics.list(project).execute();
  if (list.getTopics() == null || !list.getTopics().contains(new Topic().setName(topic))) {
      return topics.create(topic, new Topic()).execute();
  } else {
      return new Topic().setName(topic);
  }
} 
```

云或边缘提供商通常会简化这些代码。例如，Google Cloud 将主题创建简化为一行代码。

```
gcloud beta pubsub topics create topicName 
```

## 发布/订阅的例子

发布/订阅消息传递有许多用例，其中一些包括:

*   平衡工作负载
*   异步工作流
*   事件通知
*   数据流

### 法耶

Faye 是一个基于发布/订阅消息的开源系统。下面的代码向您展示了如何使用 Faye 启动服务器、创建客户端和发送消息。

```
var http = require('http'),
    faye = require('faye');

var server = http.createServer(),
    bayeux = new faye.NodeAdapter({mount: '/'});

bayeux.attach(server);
server.listen(8000);

var client = new Faye.Client('http://localhost:8000/');

client.subscribe('/messages', function(message) {
  alert('Got a message: ' + message.text);
});

client.publish('/messages', {
  text: 'Hello world'
}); 
```

Faye 比标准 JavaScript 简单得多，它是专门为 Node.js 和 Ruby 服务器创建的。它通常用于在线即时消息传递，这是大多数人每天都会遇到的发布/订阅用例。

### 边上的酒馆/订阅

正如许多边缘计算的好处一样，发布/订阅也随着边缘计算带来的速度而蓬勃发展。发布者必须向订阅者正在收听的主题发送消息，该主题有时位于物理世界的远处。要穿过一个房间，信息可能需要通过互联网交换点穿越半个世界，而物理距离总是会产生延迟。

在边缘，信息在房间内甚至在世界范围内传播的速度可以快两到四倍。

## 关键要点

*   发布/订阅消息是指发布者向主题发送消息，然后该消息被转发给订阅者。

*   发布/订阅的概念很容易理解，但每种编码和编程语言处理它的方式都不同，这使得跨所有平台学习变得有点困难。

*   在边缘，通过使用一个[网络主干](https://blog.stackpath.com/network-backbone/)和多个[存在点](https://blog.stackpath.com/point-of-presence/)，消息传递时间可以快两到四倍。