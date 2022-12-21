# 完全堆叠:重构项目

> 原文：<https://dev.to/heymarkkop/fullstacking-restructuring-the-project-3nlm>

好的，所以我去了 [@fotontech](https://dev.to/fotoncompany) 寻求一些帮助来设置 Relay，在这么多新概念、新文件和新工具的基础上，我不得不重构我们项目的结构。
所以我们基本上是从目前所学的一切开始
，我们将使用类似于 [Entria Fullstack](https://github.com/entria/entria-fullstack) 的项目结构。我们的项目将有一个主包和其他子包，如我们的 react-native 应用程序和服务器。

## 重新启动

在另一个文件夹中，运行以下命令:

```
npm init // creates new package
git init // initiate git local repository
git add . // stage all folders and files to be commited
git commit -m "main package created" // commits staged files
git remote add origin <url> // links it with remote repository
git push -u origin master // pushes commits setting upstream from master to origin
// you may use -f flag to force push to the older remote repository 
```

Enter fullscreen mode Exit fullscreen mode

## 反应原生

```
mkdir packages // creates folder packages
cd packages // move to packages folder
react-native init app // creates a new react-native project
// you can also 'expo init app' to set up a react-native project with expo
cd app
yarn start // starts app
// On another terminal, but the same folder:
react-native run-android // builds the app into your connected smartphone 
```

Enter fullscreen mode Exit fullscreen mode

你可能想看看这个指南来设置安卓系统

您还可以配置以下脚本:

```
// package.json
  "scripts": {
    "start:app": "yarn --cwd packages/app start",
    "android": "yarn --cwd packages/app/android android",
    "shake": "adb shell input keyevent 82", // shakes phone
    "test": "jest"
  } 
```

Enter fullscreen mode Exit fullscreen mode

```
// packages/app/package.json
  "scripts": {
    "start": "react-native start",
    "android": "react-native run-android",
    "test": "jest",
    "lint": "eslint ."
  } 
```

Enter fullscreen mode Exit fullscreen mode

## nodejs+kojs

```
mkdir server // in packages folder
cd server
npm init // creates a new package
yarn add koa mongoose // adds koa and mongoose dependencies
sudo service mongod start // starts mongodb service 
```

Enter fullscreen mode Exit fullscreen mode

```
// packages/server.js
const Koa = require("koa");
const mongoose = require("mongoose");

const databaseUrl = "mongodb://127.0.0.1:27017/test";
mongoose.connect(databaseUrl, { useNewUrlParser: true });
mongoose.connection.once("open", () => {
  console.log(`Connected to database: ${databaseUrl}`);
});

const app = new Koa();
app.use(async ctx => {
  ctx.body = "Hello World";
});
app.listen(3000, () =>
  console.log("Server is running on http://localhost:3000/")
); 
```

Enter fullscreen mode Exit fullscreen mode

运行`node server.js`进行测试
不要忘记`sudo service mongod start`。
我们将在`package.json`
中的`scripts`对象中添加`"start": "nodemon server.js"`，在我们的根`packages.json`中添加`"start:server": "yarn --cwd packages/server start"`，就像我们刚刚对`start:app`所做的那样

## 獴

现在我们要连接所有的东西，并在前端显示我们数据库的对象(一个订书机)。

再次创建模型文件

```
// packages/server/models/Product.js
var mongoose = require('mongoose');

const ProductSchema = new mongoose.Schema({
  title: String,
});

module.exports = mongoose.model('Product', ProductSchema); 
```

Enter fullscreen mode Exit fullscreen mode

将查询添加到`server.js`文件

```
// packages/server/server.js
const Koa = require("koa");
const mongoose = require("mongoose");
const Product = require("./models/Product");

const databaseUrl = "mongodb://127.0.0.1:27017/test";
mongoose.connect(databaseUrl, { useNewUrlParser: true });
mongoose.connection.once("open", () => {
  console.log(`Connected to database: ${databaseUrl}`);
});

const app = new Koa();

const query = () => {
  return new Promise((resolve, reject) => {
    Product.find({}, (err, res) => {
      if (err) {
        reject(err);
      }
      resolve(res);
    });
  });
};

app.use(async ctx => {
  const data = await query();
  ctx.body = data;
});
app.listen(3000, () =>
  console.log("Server is running on http://localhost:3000/")
); 
```

Enter fullscreen mode Exit fullscreen mode

最后`yarn add axios`并在`App.js`文件中添加一个 GET 钩子

```
// packages/app/App.js
import React, {Fragment, useState, useEffect} from 'react';
import axios from 'axios';
import {Text} from 'react-native';

const App = () => {
  const [title, setTitle] = useState('Loading...');
  useEffect(() => {
    const fetchData = async () => {
      const result = await axios('http://localhost:3000');
      setTitle(result.data[0].title);
    };
    fetchData();
  }, []);

  return (
    <Fragment>
      <Text>Hello World! Product: {title} </Text>
    </Fragment>
  );
};

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

现在有了`yarn start:server`和`yarn start:app`，我们应该可以在智能手机屏幕上看到我们的订书机标题“Stampler”。
我刚刚意识到我一直拼错了 stapler。

我们的库应该是这样的版本:
[full stacking-GitHub-# 9724 f4cb 35](https://github.com/Markkop/fullstacking/tree/9724f4cb3526f53efedd3188bf0a3efd348ae682)

我们现在准备开始将 Relay 和 GraphQL 添加到我们的应用程序中。由于我们变得越来越复杂，组织我们的项目以优化我们的时间是很重要的。

参考资料:
[Koa，express，node 简化版连接 MongoDB](https://developpaper.com/koa-express-node-simplified-version-connected-to-mongodb/)
[如何用 React 钩子取数据？](https://www.robinwieruch.de/react-hooks-fetch-data)
[Entria-Monorepo Playground with graph QL，React，React Native，Relay Modern，TypeScript and Jest](https://github.com/entria/entria-fullstack)