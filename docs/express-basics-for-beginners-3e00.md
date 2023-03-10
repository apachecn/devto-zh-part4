# 初学者快速基础

> 原文：<https://dev.to/ceceliacreates/express-basics-for-beginners-3e00>

当你在为期 12 周的全栈训练营时，你必须学得很快。因为我已经熟悉了 HTML、CSS 和 Javascript，所以当我们快速浏览它们时，我通常能够在我的脑海中保持这些主题的独立。然而，一旦我们过渡到后端，它就像是我脑中的狂野西部。

Node，Express，Sequelize，哪个在后端做了什么？“fs”是原生包还是安装包？我需要要求什么？我花了很多时间只是从课堂活动和以前的家庭作业中复制和粘贴，而不是完全理解我所编码的概念。我让事情运转起来，但并不真正理解*为什么*会运转。

所以我决定回去，真正研究我的代码，并描述每一行都在做什么，以及它是否与 Node、Express 或我的数据库一起工作(使用 Sequelize 或 MongoJS)。这很有启发性和启示性，最后一切都开始到位。

如果你也处于类似的情况，我建议你回去深入研究你的代码，解析出每一行要完成的任务。不再有休息和混乱！还有调试！这不再是一个在工作代码和非工作代码之间“寻找差异”的游戏。我能够实际读取错误，并理解可能是什么地方出了问题。

作为这个练习的一部分，我为 Express 创建了一个基本的、必要的函数列表。Express 是一个软件包，允许您建立到服务器的连接，并编写路径来处理传入的请求。

下面是你应该知道如何在 Express 中做的六件事，在大多数情况下，应该包含在每个运行 Express 的应用程序中。所有这些函数都可以写在一个 server.js 文件中，或者路由可以模块化为单独的。js 文件，然后在 server.js 中需要。

## 安装并要求快递

Express 不是 Node 的本机软件包，因此必须安装。因为您希望确保它包含在您的节点模块中，所以请确保在本地安装它，然后在您的服务器中需要它。

*   在项目文件夹中，在节点中运行“npm install express”

```
npm install express 
```

Enter fullscreen mode Exit fullscreen mode

*   插入“const express = require(" express ")；”在 server.js 文件中

```
const express = require("express"); 
```

Enter fullscreen mode Exit fullscreen mode

## 将 express 赋给一个 app 变量

*   插入" const app = express()；"在您的 server.js 文件中。现在，您可以使用“app”来引用您的 express 实例(也就是您正在使用的服务器)

```
const app = express(); 
```

Enter fullscreen mode Exit fullscreen mode

## 设置 app 监听指定端口

*   Insert "app.listen(PORT，function () { console.log("在端口上运行的应用"+ PORT)})

*   端口可以直接指定，也可以作为变量指定，例如。" const PORT = process . env . PORT | | 3000；"

```
const PORT = process.env.PORT || 3000;

app.listen(PORT, function() {
  console.log("App running on port" + PORT);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 设置 app 处理 JSON 和 URLencoded 请求的数据解析

*   根据 API 预期的请求类型，您需要启用中间件来解析 JSON 或 URLencoded 请求。您可以通过 app.use()实现这一点。

```
// handle URLencoded requests
app.use(express.urlencoded({ extended: true }));

// handle JSON requests
app.use(express.json()); 
```

Enter fullscreen mode Exit fullscreen mode

## 启用静态文件自动返回

*   设置应用程序自动返回静态文件，而不必为每个文件编写特定的路径(例如。包含 HTML、CSS 和客户端 JS 文件的“公共”文件夹)

```
app.use(express.static("public")); 
```

Enter fullscreen mode Exit fullscreen mode

## 处理休息请求

编写路由来处理对路径的 REST 请求(GET、POST、PUT、DELETE)。每个路由都需要一个回调函数，该函数说明如何处理请求和响应(如果适用的话)。我已经为 GET 和 POST 编写了示例回调函数，因为它们是最常见的。

### 得到

```
app.get("path, function (req, res) {

/// res.send will return the string back to the client
res.send("Hello World!")

// res.sendFile will return the file back to the client
// check out the 'path' package on npm to avoid relative path issues
res.sendFile(path.join(__dirname, "../public/index.html"))}) 
```

Enter fullscreen mode Exit fullscreen mode

### 岗位

```
 app.post("path", function (req, res) {

const request = req.body;
...
// handle database update
}).then(function (response) {
res.json(response)
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 放

```
 app.put("path, function (req, res) {

...
// handle database update
}).then(function (response) {
res.json(response)
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 删除

```
app.delete("path", function (req, res) {
...
// handle database update
}).then(function (response) {
res.json(response) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！只要您了解这些功能是如何工作的，您就对 Express 有了基本的了解，并且能够为全栈应用程序设置服务器。如果您有任何问题或建议，请告诉我！