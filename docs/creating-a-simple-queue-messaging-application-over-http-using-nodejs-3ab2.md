# 使用 NodeJs 通过 HTTP 创建简单的队列消息传递应用程序

> 原文：<https://dev.to/elasticrash/creating-a-simple-queue-messaging-application-over-http-using-nodejs-3ab2>

实际上有一百万个类似的高性能和可伸缩的应用程序。这个不是其中之一。这主要是为了大致了解我们如何设计这样一个系统。

就像标题所说的，我将使用 NodeJs，当然还有 Typescript(现在我已经懒得直接用 javascript 写任何东西了)。

要求

*   创建队列
*   将消息排队
*   将消息存储在磁盘(持久性)和内存中。
*   从队列到消息
*   写一个 e2e 测试，看看它能处理多少数据(试着打破它)

听起来很容易，确实如此，坚持是一个小例外。

因为这是一个简单的教程，而不是一个产品级的应用程序，所以我将尽可能地简化。

为了让它更有挑战性，我将完全避免任何依赖。所以我们的`package.json`将只包括`typescript`和必要的`@types/node`，它们总是让我们的生活更轻松。

从这个教程中，我希望有人会获得以下知识。

*   对创建 http 服务器的基本理解
*   节点如何与文件系统交互
*   队列是如何工作的(我希望每个人都已经知道了)

# 设置项目

我们将开始创建一个几乎最小的 web 服务，人们可以用 node 来创建它。

```
(async () => {
    const server = await http.createServer(endpoint);
    server.listen(3000);
})();

function endpoint(req: http.IncomingMessage,res: http.ServerResponse) {
    res.end('OK');
} 
```

如果我们运行这个应用程序(`tsc`构建和`node index.js`运行),我们将立即观察到我们创建了一个 web 服务(在端口 3000 上),无论我们如何点击它，它都响应正常。🎉

我个人认为这段代码中没有什么需要解释的，但为什么不呢:

*   我们创建了一个 async IIFE(立即调用的函数表达式),一旦应用程序启动，它就会立即被调用。为了让事情变得更清楚，我们创建了一个名为 endpoint 的函数，它响应 ok 并被 http.createServer 使用。

下一步是让`endpoint`函数只接受 GET 请求

*   如果请求有一个名称和消息作为 url 参数，那么它将创建一个具有该名称的队列并推送该消息。

*   如果请求只有 name 作为 url 参数，那么我们将查找具有该名称的队列，并检索最早的消息。

```
let queues: QueuePersistence = new QueuePersistence();
function endpoint(req: http.IncomingMessage,res: http.ServerResponse) {
  if (req.method === 'GET' && req.url) {
    const queryData = (url.parse(req.url, true).query) as unknown as Incoming;
    if (queryData.name && queryData.message) {
      queues.pushMessageToQueue(queryData.name, queryData.message);
      res.end('OK');
    } else if (queryData.name) {
      const message = queues.getMessageFromQueue(queryData.name);
      res.end(message);
    } else {
      res.end('query parameters are not correct');
    }
  }
} 
```

`QueuePersistence`是所有魔法发生的地方，Incoming 是允许查询参数的接口。

```
export interface Incoming {
    name: string;
    message: string;
} 
```

我将创建另一个名为 QueueStructure
的接口

```
export interface QueueStructure {
    name: string;
    messages: string[];
} 
```

最后是我们的主类

```
export class QueuePersistence {
  public pushMessageToQueue(name: string, message: string) {
  }
  public getMessageFromQueue(name: string) {
  }
} 
```

# 功能

首先，我将在 QueuePersistence 类中编写一个 helper 函数，它将接受一个文件名作为其参数。它将尝试读取该文件，如果该文件不存在，它将创建该文件并返回其内容。

```
private readFile(filename: string): Buffer | undefined {
  if (!fs.existsSync(filename)) {
    fs.writeFile(filename, '', (error) => {
      if (error) {
        console.log(error);
      }
    });
    return Buffer.from('');
  }
  try {
    return fs.readFileSync(filename);
  } catch (error) {
    console.log(error);
  }
} 
```

这里只需要注意一点。因为我不想让这个教程变得过于复杂，所以我只使用了 fs(文件系统)模块函数的同步变体。

现在我将添加一个构造函数和两个对象，在构造函数中我将调用两个函数。

```
private _queueDefinitions: string[] = [];
private _queuePersistance: QueueStructure[] = [];
constructor() {
    this.createQueueDefinitionArray();
    this.createQueuePersistance();
} 
```

*   createQueueDefinitionArray 将创建 _queueDefinitions(对象和文件)(最初两者都是空的)。当队列在定义中不存在时，将自动创建队列。
*   createQueuePersistance 将为 _queueDefinitions 数组中的每个队列创建一个文件和一个数组条目。

```
private createQueueDefinitionArray() {
  console.log('...loading queue definition');
  const body = this.readFile('queues');
  if (body) {
    this.queueDefinition = body.toString('utf8').split('\r\n');
    console.log('...loading queue definition complete');
  } else {
    console.log('...loading queue definition failed');
    process.exit(2);
  }
} 
```

```
private createQueuePersistance() {
  console.log('...loading queue persistance');
  if (this._queueDefinitions.length > 0) {
      this._queueDefinitions.forEach((def) => {
        const body = this.readFile(def);
        if (body) {
          this._queuePersistance.push({
             name: def,
             messages: body.toString('utf8').split('\r\n').reverse()
          });
        } else {
             console.log('...loading queue persistance failed');
             process.exit(2);
        }
     });
  }
  console.log('...loading queue persistance complete');
} 
```

现在所有的文件都已经在我们的系统中创建好了，所以持久性已经设置好了。

> 不使用文件系统，我可以使用文件数据库/或任何其他类型的数据库，但是就像我在开始所说的，我想完全避免库。另外，这也是一个了解文件系统操作的好机会。🎉

接下来是一个函数，它将按名称给出队列对象 in _ queuePersistance。

```
private getQueueByName(name: string): QueueStructure | undefined {
  let queue = this._queuePersistance.find(x => x.name === name);
  if (!queue) {
    const body = this.readFile(name);
    if (body) {
      queue = {
          name: name,
          messages: []
      };
      this._queuePersistance.push(queue);
      this.addToTop('queues', name);
    }
  }
  return queue
} 
```

只是一个简单的查找功能，如果我们正在寻找的队列不在那里，创建它，推动它，并返回它。

现在有一个函数叫做`addToTop`。我将把这个函数留到最后，因为在某种程度上，它是这个应用程序中最复杂的函数。

它的功能是在文件的开头添加一个新行。

最后我们几乎得到了一切，唯一缺少的是:

*   pushMessageToQueue
*   getMessageFromQueue
*   难以捉摸的 addToTop 函数

```
public pushMessageToQueue(name: string, message: string) {
    const queue = this.getQueueByName(name);
    if (queue) {
        this.addToTop(name, message);
        queue.messages.push(message);
        console.log(queue.messages);
    }
} 
```

在这一点上，以下内容变得明显

*   新消息被添加到消息数组的末尾
*   但是它们也被添加到持久性文件的开头

```
public getMessageFromQueue(name: string) {
    const queue = this.getQueueByName(name);
    if (queue) {
        const message = queue.messages[0];
        const stat = fs.statSync(name);
        fs.truncateSync(name, stat.size - message.length - 2);
        const response = queue.messages.shift();
        console.log(`${response} was requested and removed`);
        return response;
    }
} 
```

这可能需要更多的解释

*   我们从消息数组(queue.messages[0])中获取最旧的消息
*   我们得到了持久性文件的统计数据
*   我们截断了文件(意味着我们删除了文件的最后一行),我们通过计算在哪里裁剪文件来实现这一点，即

```
SIZE_OF_FILE — MESSAGE_LENGTH — 2 // this 2 is because I am adding after each message \r\n 
```

*   我们移动数组(意味着删除第一个项目并重新索引)并返回位移(这是第一个元素)

最后

```
private addToTop(filename: string, message: string) {
  const fd = fs.openSync(filename, 'r+');
  const data = fs.readFileSync(filename);
  const buffer: Buffer = Buffer.from(`${message}\r\n`);
  fs.writeSync(fd, buffer, 0, buffer.length, 0);
  fs.writeSync(fd, data, 0, data.length, buffer.length);
  fs.closeSync(fd);
} 
```

它基本上执行以下操作

*   打开文件并返回 fd(文件描述符，基本上是一个数字)
*   我们创建文件中所有数据的流。
*   我们用新消息创建一个缓冲区
*   我们在请求文件中写入我们的缓冲区
*   在写入缓冲区后，我们通过偏移缓冲区长度来附加流的剩余部分
*   我们关闭文件

是的，我知道，在文件前面添加任何东西都不会有效率，但是这样更容易理解

# 测试

我将把整个测试粘贴到这里。就我个人而言，我觉得不需要太多的评论或说明，简单的解释就足够了。

基本上我用 setInterval 解雇了一个发布者和一个订阅者。我已经将这些值设置得尽可能的低(通过尝试不同的值)，再低就开始不停地抛出错误(因为事件循环被阻塞了)。

我从 500 毫秒开始，下降到 15 毫秒以上。我不期望每秒能够处理近 120 个请求。我印象深刻。

```
import * as http from 'http';
(async () => {
    setInterval(() => {
        // create random string as a message
        const msg = Math.random().toString(36).substring(7);
        console.log(`publishing message ${msg}`);
        request(`/?name=primary&message=${msg}`);
    }, 15);
    setTimeout(() => {
        setInterval(async () => {
            const msg: any = await request(`/?name=primary`);
           console.log(`requested message ${msg.object}`);
        }, 20);
    }, 50);
})();
function request(url: string) {
  const options = {
      host: 'localhost',
      path: url,
      port: '3000',
      method: 'GET'
  };
return new Promise((resolve, reject) => {
    const request = http.request(options, (response) => {
      let str = '';
      response.on('data', (chunk) => {
          str += chunk;
      });
      response.on('end', () => {
          resolve({ request: response, object: str });
      });
      response.on('error', (error) => {
          reject(error);
      });
    });
    request.write('');
    request.end();
  });
} 
```

我希望你和我一样喜欢