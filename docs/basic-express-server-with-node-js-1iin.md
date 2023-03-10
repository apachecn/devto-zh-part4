# 带有 Node.js 的基本 Express 服务器

> 原文：<https://dev.to/devtoncho/basic-express-server-with-node-js-1iin>

原帖在 [toncho.dev](https://toncho.dev/express-basic-server/)

Express 是 Node.js 的一个 web 应用程序框架，它允许您以一种更加简单和干净的方式创建 API 和 web 服务器。它是一个轻量级的包，不会掩盖 Node.js 的核心特性。

### 创建项目

首先，创建一个目录，然后在终端
中打开它

```
$ mkdir express-basic-server
$ cd express-basic-server 
```

之后，让我们通过运行命令`npm init`初始化节点项目，并回答几个问题

```
$ npm init 
```

这将创建一个名为`package.json`的新文件，其内容与
非常相似

```
{  "name":  "express-basic-server",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1"  },  "author":  "",  "license":  "ISC"  } 
```

现在我们需要安装`express`，这非常简单，通过`npm` *(节点包管理器)*安装它，就像你安装其他包一样

`npm install express --save`

这将使用`express`依赖关系更新文件`package.json`，并将其安装在`node_moudles`文件夹
中

```
{  "name":  "express-basic-server",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1"  },  "author":  "",  "license":  "ISC",  "dependencies":  {  "express":  "^4.17.0"  }  } 
```

### 创建服务器

在 Express 中创建 HTTP 服务器非常容易。首先，我们需要创建一个文件来设置我们的服务器，最常见的名字是`app.js`和`server.js`，但是你可以随意调用

```
$ touch server.js 
```

第一行是导入我们已经安装的`express`包，这将给我们一个函数模块。

```
const express = require('express'); 
```

然后我们需要创建我们的应用程序变量。

```
const app = express(); 
```

> 注意:您可以这样创建多个应用程序，每个应用程序都有自己的请求和响应。

现在，让我们定义服务器将监听的端口，从环境变量中获取是一个很好的主意，因此可以很容易地配置，默认情况下，我们将它设置为 3000

```
const port = process.env.port || 3000; 
```

最后，我们必须启动我们的服务器！我们将端口传递给 listen 函数。该函数作为第二个可选参数传入，并在服务器启动时运行。这只是在控制台上给我们一些反馈，让我们知道我们的应用程序正在运行。

```
app.listen(port, () => {
    console.log(`Server listening on port ${port}!`);
}); 
```

现在我们有了一个基本的服务器

```
const express = require('express');

const app = express();

const port = process.env.port || 3000;

app.listen(port, () => {
    console.log(`Server listening on port ${port}!`);
}); 
```

要运行服务器，让我们使用下一个命令

```
$ node server.js 
```

或者

```
$ nodemon server.js 
```

> 注意:如果你想使用`nodemon`，你必须安装它

`node`和`nodemon`命令的主要区别在于，如果你使用节点命令，每次保存后你都要手动运行命令，但是如果你使用`nodemon`命令，它会自动检测项目中任何文件的变化。

您可以通过运行命令
来全局安装`nodemon`

```
$ npm install nodemon -g 
```

或者你可以把它作为一个 dev 依赖项安装在项目中，并设置一个脚本用`nodemon`
运行服务器

```
$ npm install nodemon --save-dev 
```

现在我们有了类似于
的`package.json`文件

```
{  "name":  "express-basic-server",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1",  "server":  "nodemon server.js"  },  "author":  "",  "license":  "ISC",  "dependencies":  {  "express":  "^4.17.0"  },  "devDependencies":  {  "nodemon":  "^1.19.0"  }  } 
```

我们可以通过运行命令
来运行服务器

```
$ npm run server 
```

### 基本路由

任何服务器的主要职责是应用程序如何通过特定的 HTTP 方法响应客户端对特定端点(如 path)的请求。在服务器端路由中，每个路由都有一个路由方法、一个路由路径和一个或多个路由处理函数。

让我们在服务器中通过 HTTP 方法`get`监听路径`/`，并用`Hello World!`
响应一个`<h1>` HTML 标签

```
app.get('/', (req, res) => {
  res.send('<h1>Hello World!</h1>')
}); 
```

现在我们的`server.js`文件看起来像

```
const express = require('express');

const app = express();

const port = process.env.port || 3000;

app.get('/', (req, res) => {
  res.send('<h1>Hello World!</h1>')
});

app.listen(port, () => {
    console.log(`Server listening on port ${port}!`);
}); 
```

现在，在运行服务器之后，您可以访问 [http://localhost:3000](http://localhost:3000) 并查看

你可以找到更多信息[快递](https://expressjs.com/)