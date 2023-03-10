# REST 速成班，GraphQL 和 Graphback #2: REST

> 原文：<https://dev.to/aerogear/technologies-that-changed-my-perception-of-software-development-34lm>

* * *

# 

<center>休息 10 分钟！</center>

* * *

在很高的层面上，REST 或表示性状态转移是一种将客户端和服务器分开的架构风格。客户端向服务器发送请求，服务器响应，而它们的关注点是分开的，这意味着客户端不知道服务器是如何工作的，它需要知道的只是从给定的 URL 访问某些数据。

另一方面，服务器不关心有多少客户机将向它请求数据。

客户端使用 [HTTP 方法](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)访问服务器，最常见的有:GET、POST、PUT、DELETE、PATCH。

下面的教程将向您展示如何构建一个典型的 REST API。我们将通过用 Express 编写一个简单的 Node.js 服务器来实现。让我们试一试！你可以在我的 [Github repo](https://github.com/MStokluska/REST-Tutorial) 里找到教程的完成版。

## 要求

*   Node.js 已安装
*   Visual Studio 代码

## 我们开始吧

*   打开您的 Visual Studio 代码(VSC)并按 ctrl +`打开终端
*   创建一个名为`server`的文件夹，并导航至该文件夹
*   在终端中键入以下内容:

```
$ npm init 
```

这将启动 npm 包创建器，请随意填写一些细节，但如果你愿意，你可以通过点击几次“回车”让它为空！

添加一个`index.js`文件，并在
中输入以下代码

```
console.log("hello world") 
```

`Note`:每次输入代码后，不要忘记点击`ctrl + s`将您的工作保存在 Visual Studio 代码中

在您的`package.json`文件中添加一个启动脚本，如果有回显脚本，您可以删除它:

```
 "scripts":  {  "start":  "node index.js"  } 
```

应该是这样的:

```
{  "name":  "server",  "version":  "1.0.0",  "main":  "index.js",  "license":  "MIT",  "scripts":  {  "start":  "node index.js"  }  } 
```

在命令行中键入:

```
$ npm start 
```

您应该在终端中看到“hello world”输出！Npm 查看 package.json 中指定的脚本并执行它们。Package.json 可以为你的项目包含很多设置和脚本，例如，license - MIT 意味着它是一个“自由软件”，任何人都可以使用它。关于 package.json，你可以学到很多有用的东西，但是我们现在不要深入研究；)如果没有我们的“开始”脚本，我们将不得不执行:

```
$ node index.js 
```

如果你感兴趣，你可以在这里找到更多关于脚本的信息。

接下来，我们将使用 Express，这是一个非常流行且易于使用的框架，用于在 Node.js 中构建 HTTP 服务器

```
$ npm install express 
```

这个命令所做的只是简单地将 express 添加到我们的依赖项中。我们需要安装 express 的原因之一是因为它允许我们以非常简单的方式使用 GET、POST、PUT、DELETE 和其他 HTTP 方法！

完成后，我们终于可以启动我们的第一台 express 服务器了！编辑 index.js，如下所示:

```
const express = require('express');

const app = express();

app.get('/_ping', (req, res) => {
  res.send('pong');
});

const port = 4000;
app.listen(port, () => {
  console.log(`Server is listening on port ${port}`);
}); 
```

*   我们只是从已经下载的 express 依赖项中导入 express
*   我们正在创建一个快递应用程序
*   这是我们第一次使用的 HTTP 方法——GET——它只是希望从给定的 URL 访问服务器时得到一些东西
*   `res.send('pong')` -在“/_ping”上访问我们的应用程序后，让我们将“pong”发送回客户端！
*   `const port` -将端口变量设置为 4000
*   `app.listen(port...)` -使用 set 变量使服务器能够监听给定端口上的请求

总而言之，我们已经设置了一个使用 express 的 Node.js 服务器。我们的服务器监听`localhost:4000`，接受来自`/_ping`的请求，并用 pong 响应。

要测试它，在您的终端运行:

```
$ npm start 
```

接下来，打开你的浏览器，进入`http://localhost:4000/_ping`，享受 Node.js 和 express 的强大！Pong 应该显示在您的浏览器中！如果要留在航站楼:

```
$ curl localhost:4000/_ping 
```

`Note`:使用`curl`如上所述启动服务器，打开另一个终端窗口，然后在这个窗口中运行你的`curl`命令。

我们的下一步是添加某种数据。为此，让我们模拟一个数据库连接。在典型情况下，我们的服务器会与 PostgreSQL、MySQL 或 Mongo 等数据库进行通信，但出于本教程的目的，让我们使用一个模拟数据库。创建一个`db.js`文件并复制下面的代码:

```
const users = [
    {
      id: '1',
      firstName: 'Michael',
      lastName: 'Stone',
      title: 'Mr',
      email: 'michael@example.com',
    },
    {
      id: '2',
      firstName: 'John',
      lastName: 'Barry',
      title: 'Mr',
      email: 'john@example.com',
    },
    {
      id: '3',
      firstName: 'Mary',
      lastName: 'Savage',
      title: 'Mrs',
      email: 'mary@example.com',
    },
  ];

  const tasks = [
    {
      id: '20',
      title: 'Restocking',
      description: 'please restock soft drinks section',
      status: 'to be completed',
      assignedTo: '1',
    },
    {
      id: '21',
      title: 'Cleaning',
      description: 'please clean your desk!',
      status: 'to be completed',
      assignedTo: '2',
    },
    {
      id: '22',
      title: 'Documentation update',
      description: 'please update our customers details',
      status: 'to be completed',
      assignedTo: '3',
    },
  ];

  module.exports = { users, tasks }; 
```

这是一个非常简单的数据库，只有几个用户和任务，每个任务都分配给某个用户。

*   因为我们现在想把实际的数据发送给我们的客户机，而不仅仅是“pong ”,所以在我们的`index.js`中添加下面几行:

```
const { users, tasks } = require('./db'); 
```

*   这允许我们从 db 文件中访问任务和用户对象，还可以将其他路由添加到我们的`index.js`:

```
app.get('/tasks', (req, res) => {
    res.json(tasks);
});

app.get('/users', (req, res) => {
    res.json(users);
}); 
```

添加新路线并重启服务器后，从浏览器访问`localhost:4000/users`或`localhost:4000/tasks`！或者在终端中，简单地输入:

```
$ curl localhost:4000/users 
```

```
$ curl localhost:4000/tasks 
```

假设我们想通过用户的名字来获取一个特定的用户。我们可以通过传入参数来做到这一点。让我们假设我们知道用户的名字，这就是我们想要找到他们的方式。将下面的 GET 方法添加到我们的`index.js` :

```
app.get('/users/:userName', (req, res) => {
    res.send(users[users.findIndex(u => u.firstName === req.params.userName)]);
  }); 
```

重启服务器，使用 curl 或访问`localhost:4000/users/Michael`试试看。

```
$ curl localhost:4000/users/Michael 
```

在您的回复中，您应该只收到关于迈克尔的详细信息。因为这个 GET 请求接受一个参数，所以您可以将 URL 更改为我们数据库中的任何其他名称，以获取所需用户的记录！在命令行中试试这个:

```
$ curl localhost:4000/users/John 
```

现在，让我们通过另一个 HTTP 方法- DELETE！为此，向我们的`index.js` :
添加另一个方法

```
app.delete('/users/:userName', (req, res) => {
    const indexOfUserToBeDeleted = users.findIndex(u => u.firstName === req.params.userName);
    users.splice(indexOfUserToBeDeleted, 1);
    res.json(users);
}); 
```

重启你的服务器，在终端输入:

```
$ curl -X "DELETE" localhost:4000/users/Michael 
```

使用 DELETE 和一段非常短的 JavaScript 代码，我们就能够从我们的用户数组中删除一个项目！

我想介绍的最后一个方法是 POST。POST 可用于向数组中输入新对象或更新现有对象。让我们用它向我们的用户数组添加一个新的用户对象！

将 POST 方法添加到`index.js` :

```
app.post('/users', (req, res) => {
  const user = req.body;
  users.push(user);
  res.json(users);
}); 
```

*   在 API 中使用 POST 方法之前，我们需要向 express 应用程序添加一个 JSON 主体解析器，因为 POST 方法将包含需要访问的 JSON 对象。要进行此安装:

```
$ npm install body-parser 
```

*   然后，我们必须在`index.js`中向我们的应用程序添加主体解析器:

```
const express = require('express');
const { users, tasks } = require('./db');
const bodyParser = require('body-parser'); <------------------

const app = express();

app.use(bodyParser.json());               <------------------
... 
```

*   重新启动服务器并运行以下 curl 命令:

```
$ curl -d '{"id":"4", "firstName":"Anne", "lastName":"OBrien", "title":"Ms", "email":"anne@example.com"}' -H "Content-Type: application/json" -X POST http://localhost:4000/users 
```

您应该会在响应中看到我们新添加的用户。

你的`index.js`应该是这样的:

```
const express = require('express');
const { users, tasks } = require('./db');
const bodyParser = require('body-parser');

const app = express();
app.use(bodyParser.json());

app.get('/_ping', (req, res) => {
    res.send('pong');
});

app.get('/tasks', (req, res) => {
    res.json(tasks);
});

app.get('/users', (req, res) => {
    res.json(users);
});

app.get('/users/:userName', (req, res) => {
    res.send(users[users.findIndex(u => u.firstName === req.params.userName)]);
});

app.post('/users', (req, res) => {
    const user = req.body;
    users.push(user);
    res.json(users);
});

app.delete('/users/:userName', (req, res) => {
    const indexOfUserToBeDeleted = users.findIndex(u => u.firstName === req.params.userName);
    return res.json(users.splice(indexOfUserToBeDeleted, 1));
});

const port = 4000;
app.listen(port, () => {
    console.log(`Server is listening on port ${port}`);
}); 
```

## 总结

这就对了。第一个使用 Express with REST 端点的 Node.js 服务器。这很简单，你可以用 REST 做更多的事情，但是我想我的目标是让你对 REST 有一个基本的了解。你刚刚完成的事情与我在成为软件开发人员的冒险之初接触到的事情类似，这一冒险仍在继续😉

它也让我大开眼界，帮助我理解客户机-服务器架构！然而，我相信您现在可以想象，您需要为想要发送的每个数据公开一个不同的端点。我们的项目只有两个实体，用户和任务，这是简单明了的，但是想象一下，如果您添加另外 10 个、20 个甚至更多需要相互交互的实体，REST 会变得多么复杂！

想象一下，我们到目前为止所做的一切都需要通过例如 SQL 语句来访问数据库。这些语句需要放在服务器的不同层来隐藏业务逻辑！想象一下，为您的“梦想”应用程序编写所有这些 REST 端点！不再那么容易了，是吗？！

在我的下一篇帖子中，让我们来了解一下 [GraphQL！](https://dev.to/aerogear/technologies-that-changed-my-perception-of-software-development-mbm)！