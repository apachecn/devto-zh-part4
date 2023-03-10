# 完整堆栈设置(Node.js、React.js 和 MongoDB)

> 原文：<https://dev.to/pacheco/my-fullstack-setup-node-js-react-js-and-mongodb-2a4k>

每当我必须创建一个新项目时，我更喜欢只使用一种语言。所以我喜欢用 javascript 做任何事情，比如 Node.js，Express.js，React.js，在这种情况下，我真的喜欢使用 NoSQL 数据库，比如 MongoDB。

所以我决定分享一下我从零开始搭建这个环境的经验。

首先，让我们创建一个文件夹，并为这个项目生成 package.json 文件。

```
$ mkdir node-react-starter
$ cd node-react-starter
$ npm init -y 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们安装项目依赖项

```
$ npm install --save express body-parser mongoose 
```

Enter fullscreen mode Exit fullscreen mode

> 在这个项目中，我们使用了 [Express.js](https://expressjs.com) ，这是一个非常流行的 Node.js 应用程序框架。
> [body-parser](https://www.npmjs.com/package/body-parser) 用于在您的处理程序之前解析中间件中的传入请求体，在 req.body 属性下可用。
> [mongose](https://www.npmjs.com/package/mongoose)是一个 MongoDB 对象建模工具，设计用于在异步环境中工作。

然后，安装开发依赖项

```
$ npm install --save-dev nodemon concurrently 
```

Enter fullscreen mode Exit fullscreen mode

> [nodemon](https://www.npmjs.com/package/nodemon) 是一个包，它运行 node.js 应用程序并监听任何文件更改，更新整个应用程序。
> 
> [并发](https://www.npmjs.com/package/concurrently)允许我们同时运行多个 npm 命令。

安装完依赖项后，您应该会得到如下所示的文件:

[![package.json file example](img/515ed74711320b528858026fb1175b19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NmBcnLXY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pvofb1ei1nn1rx5dirhf.png)

让我们创建项目结构

```
$ mkdir models routes
$ touch index.js 
```

Enter fullscreen mode Exit fullscreen mode

打开 index.js 文件，添加以下代码:

```
//  index.js

const express = require('express');
const mongoose = require('mongoose');
const bodyParser = require('body-parser');

const app = express();

mongoose.Promise = global.Promise;
mongoose.connect(process.env.MONGODB_URI || `mongodb://localhost:27017/node-react-starter`);

app.use(bodyParser.json());

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
  console.log(`app running on port ${PORT}`)
}); 
```

Enter fullscreen mode Exit fullscreen mode

之后，您可以在 package.json 文件中的 scripts:
下添加一个运行脚本

```
"server":  "nodemon index.js" 
```

Enter fullscreen mode Exit fullscreen mode

此时，您可以运行您的后端并与 mongodb 成功连接(MongoDB 必须启动并运行)。您可以像这样运行刚刚创建的脚本:

```
$ npm run server 
```

Enter fullscreen mode Exit fullscreen mode

让我们启动我们的版本控制来跟踪每一个变更。但是首先我们需要在项目的根目录下添加一个. gitignore 文件，内容如下:

```
node_modules
.idea 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们启动我们的版本控制

```
$ git init
$ git add .
$ git commit -am "first commit" 
```

Enter fullscreen mode Exit fullscreen mode

我们成功地创建了后端结构，现在让我们跳到前端。

现在，让我们用 [create-react-app](https://github.com/facebook/create-react-app) 创建一个 React 应用。

```
$ create-react-app client 
```

Enter fullscreen mode Exit fullscreen mode

现在，在客户端目录中，我们必须添加我们的依赖项。
这里我们要用[纱](https://www.npmjs.com/package/yarn)来添加这个依赖关系。

```
$ cd client
$ yarn add axios 
```

Enter fullscreen mode Exit fullscreen mode

> axios 是一个非常流行的基于 promise 的 HTTP 客户端，用于浏览器和 node.js。

## 为反应脚本> = 0.2.3

对于当前的 react 版本(以及任何其他大于 0.2.3 的 react-scripts)，您可以简单地将下面一行添加到客户端目录中的 package.json 文件**中，这将允许您将前端请求代理到后端应用程序。** 

```
"proxy":  "http://localhost:5000" 
```

Enter fullscreen mode Exit fullscreen mode

## 为反应脚本< 0.2.3

如果您使用的是旧版本的 react-scripts，您可能需要添加以下配置来连接前端和后端:

```
$ cd client
$ yarn add http-proxy-middleware 
```

Enter fullscreen mode Exit fullscreen mode

> [http-proxy-middleware](https://www.npmjs.com/package/http-proxy-middleware) 用于在开发过程中创建从 react 应用到后端应用的代理。

我们现在可以添加配置文件来设置代理，以便从前端向后端应用程序发出请求。
**记住，只有在您使用较旧的 react 版本，即 react-scripts < 0.2.3 时，才添加这个配置。**

在目录/client/src 中，添加文件 setupProxy.js，内容如下

```
// /client/src/setupProxy.js

const proxy = require('http-proxy-middleware')

module.exports = function(app) {
    app.use(proxy('/api/*', { target: 'http://localhost:5000' }))
} 
```

Enter fullscreen mode Exit fullscreen mode

在项目的根目录下的 package.json 中，我们添加以下运行脚本:

```
"client": "npm run start --prefix client",
"server": "nodemon index.js",
"dev": "concurrently --kill-others-on-fail \"npm run server\" \"npm run client\"",
"start": "node index.js" 
```

Enter fullscreen mode Exit fullscreen mode

现在你的 package.json 文件应该是这样的:

```
{  "name":  "node-react-starter",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1",  "client":  "npm run start --prefix client",  "server":  "nodemon index.js",  "dev":  "concurrently --kill-others-on-fail \"npm run server\"  \"npm run client\"",  "start":  "node index.js"  },  "keywords":  [],  "author":  "",  "license":  "ISC",  "dependencies":  {  "body-parser":  "^1.19.0",  "express":  "^4.17.1",  "mongoose":  "^5.6.3"  },  "devDependencies":  {  "concurrently":  "^4.1.1",  "nodemon":  "^1.19.1"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以使用以下命令运行项目:

```
$ npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

这将在端口 5000 上运行后端应用程序，在端口 3000 上运行前端应用程序。
您应该看到 react 应用程序在 [http://localhost:3000](http://localhost:3000) 上运行

为了准备好项目生产，我们需要在 index.js 文件中添加以下代码行，就在 app.use(bodyParser.json())调用之后:

```
if (process.env.NODE_ENV === 'production') {
  app.use(express.static('client/build'));

  const path = require('path');
  app.get('*', (req,res) => {
      res.sendFile(path.resolve(__dirname, 'client', 'build', 'index.html'))
  })

} 
```

Enter fullscreen mode Exit fullscreen mode

这将把所有请求重定向到我们的前端应用程序，除非我们在这段代码之前指定了任何路由。

现在让我们创建一个简单的交互来查看代理连接的运行情况

在目录/models 中添加 Product.js 文件，并插入以下代码:

```
// /models/Product.js

const mongoose = require('mongoose');
const {Schema} = mongoose;

const productSchema = new Schema({
    name: String,
    description: String,
})

mongoose.model('products', productSchema); 
```

Enter fullscreen mode Exit fullscreen mode

让我们为我们的后端 API 创建一个路由。

在目录/routes 中添加 productRoutes.js 文件，并插入以下代码:

```
// /routes/productRoutes.js
const mongoose = require('mongoose');
const Product = mongoose.model('products');

module.exports = (app) => {

  app.get(`/api/product`, async (req, res) => {
    let products = await Product.find();
    return res.status(200).send(products);
  });

  app.post(`/api/product`, async (req, res) => {
    let product = await Product.create(req.body);
    return res.status(201).send({
      error: false,
      product
    })
  })

  app.put(`/api/product/:id`, async (req, res) => {
    const {id} = req.params;

    let product = await Product.findByIdAndUpdate(id, req.body);

    return res.status(202).send({
      error: false,
      product
    })

  });

  app.delete(`/api/product/:id`, async (req, res) => {
    const {id} = req.params;

    let product = await Product.findByIdAndDelete(id);

    return res.status(202).send({
      error: false,
      product
    })

  })

} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以导入 index.js 中的模型和路径文件，如下所示:

```
// /index.js
const express = require('express');
const mongoose = require('mongoose');
const bodyParser = require('body-parser');

// IMPORT MODELS
require('./models/Product');

const app = express();

mongoose.Promise = global.Promise;
mongoose.connect(process.env.MONGODB_URI || `mongodb://localhost:27017/node-react-starter`);

app.use(bodyParser.json());

//IMPORT ROUTES
require('./routes/productRoutes')(app);

if (process.env.NODE_ENV === 'production') {
  app.use(express.static('client/build'));

  const path = require('path');
  app.get('*', (req,res) => {
      res.sendFile(path.resolve(__dirname, 'client', 'build', 'index.html'))
  })

}

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
  console.log(`app running on port ${PORT}`)
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们运行项目，我们就能够使用 URL[http://localhost:5000/api/product](http://localhost:5000/api/product)向简单的产品 API 发出请求。
在这里我们可以获取、插入、更新和删除产品。

回到 react 应用程序，让我们添加一个向后端应用程序发出请求的服务。
在文件夹/client/src 中创建一个名为 services 的文件夹，并添加一个文件 productService.js，内容如下:

```
//  /client/src/services/productService.js

import axios from 'axios';

export default {
  getAll: async () => {
    let res = await axios.get(`/api/product`);
    return res.data || [];
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们编辑 App.js 文件，添加一个显示产品列表的简单 UI:

```
// /client/src/App.js

import React, { useState, useEffect } from "react";

// SERVICES
import productService from './services/productService';

function App() {
  const [products, setproducts] = useState(null);

  useEffect(() => {
    if(!products) {
      getProducts();
    }
  })

  const getProducts = async () => {
    let res = await productService.getAll();
    console.log(res);
    setproducts(res);
  }

  const renderProduct = product => {
    return (
      <li key={product._id} className="list__item product">
        <h3 className="product__name">{product.name}</h3>
        <p className="product__description">{product.description}</p>
      </li>
    );
  };

  return (
    <div className="App">
      <ul className="list">
        {(products && products.length > 0) ? (
          products.map(product => renderProduct(product))
        ) : (
          <p>No products found</p>
        )}
      </ul>
    </div>
  );
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

此时，您可以使用命令 npm run dev 再次运行该应用程序，您将看到以下屏幕:

[![](img/4e7284e9bf0a088fca238b610eb58e87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gaX_gM_T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xfa4yz3gbmlg4r9s3qh9.png)

使用类似[邮差](https://www.getpostman.com/)或[失眠](https://insomnia.rest/)的 HTTP 客户端添加一些产品。用以下 JSON 内容向[http://localhost:5000/API/product](http://localhost:5000/api/product)发出 POST 请求:

```
{  "name":  "<product name>",  "description":  "<product description here>"  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，您将能够看到屏幕上呈现的产品列表，如下所示:

[![](img/a63699ada9a4d6fb0621b9d5d2687969.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5kULsou5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8n282icdca6e01esu7eb.png)

我希望你会发现这个教程很有用，在接下来的日子里，我会继续这个教程，展示如何 Dockerize 这个应用程序。

也请查看下一篇解释如何将这个应用程序部署到 heroku 的帖子。

如果你对使用容器感兴趣，我也发表了这篇文章，解释了如何将这个应用 dockerize 并部署到 Heroku 。

源代码可以在[这里](https://github.com/thisk8brd/node-react-starter)找到