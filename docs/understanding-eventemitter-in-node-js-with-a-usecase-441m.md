# 通过用例了解 Node.js 中的 EventEmitter

> 原文：<https://dev.to/ganeshmani/understanding-eventemitter-in-node-js-with-a-usecase-441m>

在本文中，我们将通过理解一些用例来了解什么是事件发射器以及它是如何工作的。通过用例了解 Node.js 中的 EventEmitter

最近关于 Node.js 的文章，

[面向 Node.js 开发者的 Apache Kafka】](https://cloudnweb.dev/2019/08/apache-kafka-for-node-js-developers/)

Node.js 的核心概念之一是[事件](https://nodejs.org/api/events.html)。事件发射器在 Node.js 事件驱动架构中起着至关重要的作用

EventEmitter 是一个促进 Node.js 中对象之间通信的模块。在 Node.js 上构建的大多数库/模块都使用 Event Emitter，因为 Node.js 遵循事件驱动的架构。

也就是说，我们将在本文中通过使用事件发射器构建自定义记录器来学习事件发射器。

### 它是如何工作的？

*   事件发射器在名为**消息**的事件中发出数据
*   在事件**消息**上登记了一个监听
*   当**消息**事件发出一些数据时，监听器会得到这些数据。

[![](img/5847092a57a7bbb8c1c8e6c90da41338.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XZljKYPH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloudnweb.dev/wp-content/uploads/2019/08/eventEmitter_works.png)

### 积木

*   **。emit()** -事件发射器中的这个方法是在模块中发出一个事件
*   **。on()** -这个方法监听 node.js 中注册事件的数据
*   **。once()** -它只监听一次注册事件数据。
*   。addListener () -检查监听器是否注册了事件。
*   。删除监听器 () -删除事件的监听器。

[![](img/e9ecd6e5d7aeb67fbce2ea299f997c4a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4KY-Boju--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloudnweb.dev/wp-content/uploads/2019/08/eventemiitter.png)

```
const EventEmitter = require('events');

const emitter = new EventEmitter();

emitter.on('message',function(message) {

  console.log("subsribed",message);
})

emitter.emit('message',{ data : "Data" });
```

首先，导入名为 **events** 的包，它基本上是一个类。之后，实例化名为**发射器**的类。

之后，我们需要为特定事件注册侦听器。

最后，当我们发出一个事件时，注册的监听器将获得事件和数据。

### 使用案例

如前所述，事件发射器是 node.js 中的一个重要概念。我们了解了它的工作原理和重要的构建模块。

但是，当我们学到一些东西时，我们脑海中出现的一个重要问题是，我需要在哪里实现它？。

让我们看一个用例来了解更多关于事件发射器的信息。

### 使用事件发射器的记录器

主要是，我们将看到如何使用事件发射器编写一个日志记录函数。

创建一个名为 **logger.js** 的文件，并添加以下代码。

```
const EventEmitter = require('events');

class Logger extends EventEmitter {

   logIt(eventName,message) {

      this.emit(eventName,message);
   }
}

module.exports = Logger
```

类 **Logger** 扩展了 **EventEmitter** 类。添加以下名为 **logIt** 的方法，该方法将事件名称和消息作为参数。

创建一个名为 **index.js** 的文件，并添加以下代码。

```
 const Logger = require('./Logger');

const logger = new Logger();

logger.on('logMessage',(arg) => {
   console.log("Listened value => ",arg);
})

logger.logIt('logMessage',{ name : 'John',age : 40 })
```

它需要模块 **Logger** ，并将该类实例化为 **logger** 。

**logIt()** 函数发出名为 **logMessage** 的事件。监听器将监听事件并记录下来。

为了学习 Node.js 中的一些高级概念，我最喜欢的课程之一是来自 [Stephen Grider](https://www.udemy.com/user/sgslo/) 。我推荐这个课程来学习 Node.js 中的高级概念

[节点 JS:高级概念](https://click.linksynergy.com/link?id=YIJ5oggSvpI&offerid=507388.1587718&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Fadvanced-node-for-developers%2F) [![](img/ed14a71cded931afd9103cf83ce100f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NBhMqqmc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ad.linksynergy.com/fs-bin/show%3Fid%3DYIJ5oggSvpI%26bids%3D507388.1587718%26type%3D2%26subid%3D0)