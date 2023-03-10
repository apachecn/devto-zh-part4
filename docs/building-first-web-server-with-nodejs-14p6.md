# 使用 NodeJS 构建第一个 web 服务器

> 原文：<https://dev.to/uddeshjain/building-first-web-server-with-nodejs-14p6>

这是这个系列的第一部分。在这个系列中，我们将从 NodeJS 的基础开始，逐步构建一个具有所有特性的 Restful API，比如 T2 认证、T4 数据库集成等等。

现在出现在脑海中的第一个问题是**为什么节点？**

NodeJS 是 **JavaScript** 的运行时环境，所以已经熟悉 **JavaScript** 的开发者可以很容易地学习和使用 **NodeJS** 。
另一个好处是**单线程事件循环**，它负责从外部请求中提取 I/O。还有很多原因，但我认为这两个就足够了。

现在，不浪费任何时间，我们将直接开始构建一个令人惊叹的 restful API。

我们将使用 [express](https://expressjs.com/) 来构建这个 API。如果你对 *express* 有任何疑问，请访问他们的主页并尝试阅读文档。

# 环境设置

1.  下载并安装 [NodeJS](https://nodejs.org/en/download/) 。如果您感到困惑，请选择 LTS(长期支持)版本。

2.  下载并安装你最喜欢的代码编辑器。我更喜欢 [VSCode](https://code.visualstudio.com/)

3.  创建项目目录。

4.  打开终端(windows 用户可以使用 git bash)，浏览到项目目录并编写 *npm install express* 并点击 enter。

5.  暂时就这样了。

# 你好世界中的节点

因为您已经熟悉了 JavaScript hello worlds。节点也是如此。

```
console.log('Hello World') 
```

Enter fullscreen mode Exit fullscreen mode

# 第一个 Web 服务器与节点和 express

```
const express = require('express')
const app = express()

app.get('/', (req, res) => {
    res.send('Hello World')
})

const port = process.env.PORT || 3000
app.listen(port, () => console.log(`App is listning on port ${port}`)) 
```

Enter fullscreen mode Exit fullscreen mode

### 交代

在上面代码的第一行，我们导入了 *express* 模块，该模块返回一个函数，我们将该函数存储在一个名为 **express** 的变量中。在第二行中，我们调用了返回一个对象的 **express** 函数，并将它存储在一个名为 *app* 的变量中。之后，我们编写了处理 *GET* 请求的代码。这个 get 方法有两个参数。

1.  路径或 URL，即 *'/'* (网站的根目录)

2.  一个回调函数，当我们有一个 *HTTP* *GET* 请求时将被调用。现在，这个回调有两个参数 *req* 即请求和 *res* 即响应。响应用于发送我们想要的响应。

现在有许多不同类型的请求，我们将在接下来的帖子中讨论它们。

在最后两行中，我们设置了一个端口来监听请求。我们在 listen 方法中传递了端口号和一个可选的回调函数(它将向控制台记录一条消息)。

现在检查工作是否正常

*   运行程序*节点*

*   打开你最喜欢的浏览器，进入 *localhost:3000* 。你会看到一个 **Hello World** ，表示一切正常。

最后**祝贺**您已经成功地创建了一个能够响应您的 get 请求的 web 服务器。

快乐学习。

[![See you soon](img/de50156989e82684a1f5f5560163cf54.png)](https://i.giphy.com/media/26vUF60UHCAnsXOpy/giphy.gif)