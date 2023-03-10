# 动态要求快速应用程序中的所有路线

> 原文：<https://dev.to/rubiin/dynamically-requiring-all-routes-in-express-app-1625>

如果您正在使用 express framework 开发 web 应用程序，您可能熟悉路由的概念以及它如何在您的应用程序中发挥重要作用。路由是指应用程序的端点(URIs)如何响应客户端请求。有关路由的介绍，请参见基本路由。使用对应于 HTTP 方法的 Express app 对象的方法来定义路由；
比如 app.get()处理 get 请求，app.post 处理 post 请求。完整列表见 app.METHOD，也可以用 app.all()处理所有 HTTP 方法，用 app.use()指定中间件作为回调函数(详见使用中间件)。
我喜欢把我的路线分割成单独的文件，并且喜欢把它们放在根 js 文件中。这不仅提高了代码的可读性，还使调试变得更加容易，因为可以很容易地确定文件中的确切位置，而不是遍历单个文件中的大量代码。
要使用您的片段化路线，您必须要求它们类似于 nodejs 模块，并将其放在 app.use 中。如果您的 routes 文件夹有几个路由，这很简单，但是如果您正在讨论一个大规模的 webapp，可能会有十几个或更多的路由文件，这意味着您必须需要每个文件，并且 app.js 文件会填充重新请求路由的代码。
此外，如果有多条路线，您可能会忘记使用其中一条路线。
所以一个快速的方法是使用动态路由，它会自动要求你所有的路由都在 routes 文件夹中，这样就减少了代码，并且
要求路由更容易。

```
/**
* Dyanmic Routing
* Adds all routes from routes folder
*/
fs.readdir("./routes", (err, files) => {
files.forEach(file => {
app.use("/", require("./routes/" + file))
});
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们使用文件系统模块从 routes 文件夹中读取所有文件，并在 express app 的实例中使用它们。