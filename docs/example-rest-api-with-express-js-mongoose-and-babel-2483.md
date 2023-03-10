# 带有 Express.js、Mongoose 和 Babel 的 REST API 示例

> 原文：<https://dev.to/dwipr/example-rest-api-with-express-js-mongoose-and-babel-2483>

*阅读[我的博客](https://antaraksi.com/example-rest-api-that-uses-express-js-mongoose-and-babel/)。*

本文将讨论一个使用 Express.js、Mongoose 和 Babel 制作 REST API 的例子。创建的资源或端点仅为用户。我们只会做一个简单的用户注册方法。我们将使用存储库模式。

*   安装软件包

```
 yarn add express mongoose mongod
  yarn add --dev @babel/cli @babel/core @babel/node @babel/preset-env nodemon 
```

*   创建文件夹结构

```
.
├── package.json
├── src
│   ├── controllers
│   │   └── UserController.js
│   ├── index.js
│   ├── models
│   │   ├── repositories
│   │   │   └── UserRepository.js
│   │   └── User.js
│   └── routes
│       ├── index.js
│       └── users.js
└── yarn.lock

5 directories, 8 files 
```

*   用户模型`User.js`

```
import mongoose from 'mongoose';

const { Schema } = mongoose;

const schema = new Schema({
  email: {
    type: String
  },
  password: {
    type: String
  }
});

const User = mongoose.model('User', schema);
export default User; 
```

*   用户存储库`UserRepository.js`

在模型文件夹中创建一个`repositories`文件夹，然后创建`UserRepository.js`

```
import User from '../User';

class UserRepository {

  constructor(model) {
    this.model = model;
  }

  create(object) {
    return this.model.create(object);
  }
}

export default new UserRepository(User); 
```

然后在`controllers`文件夹中创建`UserController.js`。

*   用户控制器`UserController.js`

```
import UserRepository from '../models/repositories/UserRepository';

function createUser(req, res) {
  const user = req.body;

  UserRepository.create(user)
    .then((newUser) => {
      res.json(newUser);
    }).catch((errors) => {
      res.status(500).json({
        errors,
      });
    });
}

export default { createUser }; 
```

之后，我们将`routes`文件夹中的路线列为`users.js`

*   用户路线`users.js`

```
import express from 'express';
import UserController from '../controllers/UserController';

const router = express.Router();

router.post('/', UserController.createUser);

export default router; 
```

以及路线上的 index . js

```
import express from 'express';
import users from './users';

const router = express.Router();

router.use(express.json());

router.use('/users', users);

export default router; 
```

最后我们在`src`中创建`index.js`

*   服务器`index.js`

```
import express from 'express';
import mongoose from 'mongoose';

import routes from './routes';

mongoose.Promise = global.Promise;

const app = express();

app.use('/', routes);

mongoose.connect('mongodb://localhost:27017/db')
  .then(() => {
    console.log('mongodb started.');
    app.listen(8000, () => {
      console.log('Server started on 8000');
    });
  }).catch(() => {
    console.log('Mongodb connection failed.');
  }) 
```

*   运行服务器的命令

```
nodemon -w src -x "babel-node src --presets @babel/env" 
```

我们可以把它加到`package.json`
中的`npm-scripts`

```
{
  "name": "exercise",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "dependencies": {
    "express": "^4.17.1",
    "mongodb": "^3.2.7",
    "mongoose": "^5.6.2",
    "nodemon": "^1.19.1"
  },
  "devDependencies": {
    "@babel/cli": "^7.4.4",
    "@babel/core": "^7.4.5",
    "@babel/node": "^7.4.5",
    "@babel/preset-env": "^7.4.5"
  },
  "scripts": {
    "start": "nodemon -w src -x \"babel-node src --presets @babel/env\""
  }
} 
```

那是在用一个简单的 REST API 练习 express.js、mongoose 和 babel。完整的代码可以在 https://github.com/yoiso/exercise-express 看到