# 如何用来自 NodeJS 的随机数据模拟 API

> 原文：<https://dev.to/mrfrontend/how-to-mock-an-api-with-random-data-from-nodejs-dda>

作为前端开发人员，您经常需要来自 API 的数据。但是有时候你的团队还没有建立后端。为了让您继续模拟您的数据，最好不要将您的数据结构存储在内存中。

尽快开始与数据来源的外部 API 对话是一个好主意。在本教程中，我想通过一个最小的设置来模拟你的数据。之后，您应该能够根据需要用自己的数据扩展它。

## 依赖关系

为了使用 Node，您需要在计算机上安装它。对于 mac 用户来说，我强烈建议安装 NVM，因为这样将来更新 NodeJS 会更容易。(*WindowsT5 也有[版本)。](https://github.com/coreybutler/nvm-windows#node-version-manager-nvm-for-windows)*

为此项目创建一个新文件夹。运行`npm init -y`用 Node 初始化文件夹，它会自动为你创建一个 package.json。

为了创建 mockserver，我们需要 2 个 npm 依赖项。 [json-server](https://www.npmjs.com/package/json-server) 和 [casual](https://www.npmjs.com/package/casual) 所以我们在项目中运行`npm install json-server casual --save-dev`。

## 为模拟服务器的基础

创建一个`index.js`并将这段代码粘贴到其中。

```
const jsonServer = require('json-server')
const server = jsonServer.create()
const middlewares = jsonServer.defaults()
const port = process.env.PORT || 3000

server.use(jsonServer.bodyParser)
server.use(middlewares)

server.listen(port, () => {
    console.log('JSON Server is running')
}) 
```

为了使用它，我们包含了 **json-server** 。然后我们在`const server`中创建一个服务器实例。有了中间件，我们可以设置一些选项，比如静态文件的路径，CORS 等等。但是在这里我们只是使用它，没有具体的选项。

港口非常重要。如果您想让它在服务器上运行，它将首先搜索是否有为节点服务器设置的默认端口，否则它将选择端口`3000`。

我们通过使用`server.user()`包含了 bodyParser 和 middleswarses。然后我们做一个控制台日志，让你知道模拟服务器正在运行。

## 为 100 个用户生成数据

创建一个文件夹`/users`并在其中创建一个`index.js`。

首先包含 npm 包 casual 以便使用它。

```
const casual = require('casual') 
```

对于特定语言，如果不想要英语默认:

```
const casual = require('casual').nl_NL 
```

在它下面，我们需要导出一个模块，以便以后在根中的 de index.js 中使用它。

```
module.exports = () => {
    casual.define('user', function() {
        return {
            name: casual.first_name,
            surname: casual.last_name,
            address: casual.street,
            phone: casual.phone,
            email: casual.email,
            postalCode: casual.zip,
            city: casual.city,
            number: casual.building_number,
            id: casual.uuid,
        }
    })

    const data = {
        users: [],
    }

    // Create 100 users
    for (let i = 0; i < 100; i++) {
        data.users.push(casual.user)
    }
    return data
} 
```

使用 casual.define，我们定义了一个类型为“user”的对象。因此，为了一次为 100 个用户创建数据，我们创建了一个 for 循环，该循环将运行 100 次，并将用户数组中的一个用户推入我们的数据对象中。

之后，我们返回整个对象，这样我们就可以在根 index.js 中使用它。

## 创建一个/用户端点

为了获得 100 个用户的数据，我们需要为模拟服务器创建一个端点。将下面的代码粘贴到根 index.js 中的`server.listen()`函数之前。

```
server.get('/users', (request, response) => {
    if (request.method === 'GET') {
        const users = require('./users/index')
        response.status(200).jsonp(users())
    }
}) 
```

在这种情况下，我们使用`server.get()`作为 get 请求。但是我们也可以选择“发布”、“上传”、“删除”等等。

在内部，我们检查请求是否是一个“GET”请求。如果是这样，我们需要用户的脚本并调用响应中的函数，这样您将看到随机生成的用户数组。

## 运行模拟服务器

现在，我们能够运行模拟服务器，并在前端应用程序中获取数据。

在项目的根文件夹中运行`node index.js`。访问 [localhost:3000/users](http://localhost:3000/users) ，您将在用户数组中看到 100 个用户。

我希望本教程为您生成更多随机数据和扩展您的模拟服务器奠定了基础。如果你有任何问题，请在评论中告诉我。