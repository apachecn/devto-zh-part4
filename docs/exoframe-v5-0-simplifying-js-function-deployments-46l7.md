# Exoframe v5.0 -简化 js 函数部署

> 原文：<https://dev.to/yamalight/exoframe-v5-0-simplifying-js-function-deployments-46l7>

Exoframe 是一个自托管工具，允许使用 Docker 进行简单的单命令部署。
如果你从未听说过它，你可以在这里阅读更多[或在这里](https://hackernoon.com/simplifying-docker-management-with-exoframe-9275e92c7406)看它的行动。

今天，我很高兴地发布了 Exoframe v5.0 ，它增加了对一个命令中简单 javascript 函数部署的支持。

目前，Exoframe 支持 4 种 javascript 函数:

*   HTTP 函数——典型的 HTTP 请求处理器
*   工作者功能-任何必须在后台运行的功能(例如，机器人、重复任务等)
*   触发函数——您自己的可以调用自定义函数的触发器
*   自定义函数-自定义触发器的处理程序

这里有一个演示创建每种类型的函数的快速示例演练。

### 创建 HTTP 功能

首先，我们来写函数本身。
Exoframe 将`require`整个函数文件夹，所以确保调用你的文件`index.js`或者包含指向你的主文件的`package.json`。
在这种情况下，让我们`touch index.js`并在其中编写以下代码:

```
// index.js
module.exports = async (event, context) => {
  // use context.log to provide logs to exoframe
  // those logs can be then accessed from exoframe CLI
  context.log('test log');
  context.log('other log');

  // you can just return a value
  return `hello world`;

  // alternatively you can use reply prop
  // to directly access Fastify reply object
  context.reply.code(200).send('hello world!');
  // make sure to return false-y value if you do this
  // so exoframe doesn't try to send the response second time
  return false;
}; 
```

注意`context.log`用于记录的用法。这样做将允许您使用`exoframe logs your-function-name`命令从函数中获取日志。

接下来，我们需要为您的函数生成新的外部框架配置。为此，只需执行:

```
$ exoframe init -f 
```

这将生成如下所示的配置:

```
{  "name":  "test-function",  "function":  true  } 
```

默认情况下，Exoframe 认为函数是 HTTP 函数，并将它们路由到`/${config.name}`——因此，在我们的例子中是`/test-function`。
这可以通过编辑配置来更改，以包括`route`属性，例如:

```
{  "name":  "test-function",  "function":  {  "route":  "test"  }  } 
```

现在你只需运行`exoframe deploy`就可以使用 Exoframe 部署你的函数了！

### 创建工作者功能

还是那句话，让我们从函数本身开始。
让我们`touch index.js`并在其中编写以下代码:

```
// index.js
module.exports = async (_, context) => {
  // use context.log to log stuff, just as in HTTP function
  context.log('Worker started.');
  // worker can execute any long-running task you want
  let counter = 0;
  setInterval(() => {
    context.log(`Worker: ${counter++}`);
  }, 1000);
}; 
```

之后，我们希望以与 HTTP 函数相同的方式生成 Exoframe 配置，但是我们需要修改它以包含新的`type`属性，就像这样:

```
{  "name":  "test-worker",  "function":  {  "type":  "worker"  }  } 
```

这将告诉 Exoframe 它应该在一个单独的[工作线程](https://nodejs.org/api/worker_threads.html)中启动当前函数。
现在您的工人功能已经准备好部署了！

### 创建触发功能

同样，让我们从函数本身开始。
让我们`touch index.js`并在其中编写以下代码:

```
module.exports = async (dispatchEvent, context) => {
  // log
  context.log('Trigger started.');

  // in this case we trigger all subscribed functions every 1s
  const interval = setInterval(() => {
    context.log(`Triggering!`);
    // dispatching new events to all function with data
    dispatchEvent({data: 'hello world!'});
  }, 1000);

  // trigger function should return a cleanup function
  return () => {
    clearInterval(interval);
  };
}; 
```

请注意，您的触发器需要返回一个清理函数，当您从服务器上删除该函数时，该函数会进行清理。

之后，您需要生成一个配置文件，并编辑它以包含设置为`trigger`的`type`属性，比如:

```
{  "name":  "test-trigger",  "function":  {  "type":  "trigger"  }  } 
```

就这样，你的扳机准备好了！

### 创建自定义处理函数

最后，让我们看看如何为触发器创建自定义处理程序。
我们再把那个`index.js`写一遍:

```
module.exports = async (event, context) => {
  // Will get custom data from trigger above, so logging will say:
  // Custom function triggered: {"data": "hello world!"}
  context.log(`Custom function triggered: ${JSON.stringify(event.data)}`);
}; 
```

现在我们需要告诉 Exoframe，我们当前的函数应该对我们的自定义触发器做出反应。这是通过使用`type`属性并赋予一个与我们的自定义触发器名称相等的值来实现的，比如:

```
{  "name":  "test-triggered-fn",  "function":  {  "type":  "test-trigger"  }  } 
```

现在您可以部署您的自定义处理程序了！

### 就是这样！

这涵盖了在 Exoframe 中使用函数的所有基础知识。
你可以在[外框架文档](https://github.com/exoframejs/exoframe/blob/master/docs/Functions.md)中找到更详细的信息。

尝试一下并告诉我你的想法！