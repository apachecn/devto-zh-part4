# NodeJS & PostgreSQL:如何将我们的数据库连接到我们的 Simple Express 服务器(使用 ORM)

> 原文：<https://dev.to/miku86/nodejs-postgresql-how-to-connect-our-database-to-our-simple-express-server-with-an-orm-gcm>

## [T1】简介](#intro)

我们了解到:

*   [如何使用 PostgreSQL 做事情](https://dev.to/miku86/postgresql-what-is-it-how-to-install-it-16hk)
*   [如何用 express 创建服务器](https://dev.to/miku86/nodejs-how-to-create-a-simple-server-using-express-1n9d)
*   [如何用 pg 连接 express 和 PostgreSQL】](https://dev.to/miku86/nodejs-postgresql-how-to-connect-our-database-to-our-simple-express-server-without-an-orm-10o0)

现在我们想学习如何用`sequelize`作为 [ORM](https://stackoverflow.com/q/1279613) 连接我们的 PostgreSQL 数据库和我们的 express 服务器。

* * *

## 创建一个项目文件夹，做`npm init`，安装所有需要的包

```
mkdir node-postgres-sequelize
cd node-postgres-sequelize
npm init
npm i express sequelize pg pg-hstore 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到`sequelize`需要`pg`。

* * *

## 创建`index.js`并设置一个小型服务器

```
const express = require('express');
const app = express();
const PORT = 8080;

app.get('/', (req, res) => {
  res.send({ message: 'endpoint working' });
});

app.listen(PORT, () => {
  console.log(`Server running at: http://localhost:${PORT}/`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

用`node index.js`、
测试你的服务器，它应该运行在 [localhost:8080](http://localhost:8080)

* * *

## 创建本地数据库，表&条目

```
sudo -iu postgres
createdb -O postgres node-postgres-sequelize;
psql -d node-postgres-sequelize
\conninfo
CREATE TABLE users(id SERIAL PRIMARY KEY, nickname TEXT NOT NULL);
INSERT INTO users(nickname) VALUES ('miku86'); 
```

Enter fullscreen mode Exit fullscreen mode

用`SELECT * FROM users;`、
测试你的数据库，应该还有`1 row`。

* * *

## 只用数据库的东西创建`database.js`

```
const Sequelize = require('sequelize');
// your credentials
DATABASE_URL = 'postgres://[db-user]:[password]@127.0.0.1:5432/node-postgres-sequelize';

const database = new Sequelize(DATABASE_URL);

module.exports = database; 
```

Enter fullscreen mode Exit fullscreen mode

注意:这个实现非常简单，不用于生产。您不应该将您的凭证移动到这个文件中，您应该使用类似于 [`dotenv`](https://www.npmjs.com/package/dotenv) 的东西。但是为了简单起见，我尽可能地简化这个例子。

* * *

## 用一种方法创建`User.js`来读取所有数据库行

```
const Sequelize = require('sequelize');
const database = require('./database');

const User = database.define(
  'users',
  {
    nickname: {
      type: Sequelize.TEXT
    }
  },
  { timestamps: false }
);

User.readAll = async (req, res) => {
  try {
    const users = await User.findAll();
    return res.send({ users });
  } catch (error) {
    return res.send(error);
  }
};

module.exports = User; 
```

Enter fullscreen mode Exit fullscreen mode

我们将`User`模型和它的`readAll()`函数分开，以使事情更加明显。我们不必编写 SQL 查询(如`SELECT * FROM users`，我们可以使用对它的`sequelize`、[、](https://sequelize.org/master/manual/querying.html)。我们不关心`sequelize`是如何做到这一点的。

* * *

## 用新路线更新`index.js`

我们现在必须将`users`端点添加到`index.js`中。

```
const express = require('express');
// new: import User
const User = require('./User');  
const app = express();
const PORT = 8080;

app.get('/', (req, res) => {
  res.send({ message: 'endpoint working' });
});

// new: route to users, that runs readAll()
app.get('/users', User.readAll);

app.listen(PORT, () => {
  console.log(`Server running at: http://localhost:${PORT}/`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

确切的说有些文件像是在[教程中没有`sequelize`](https://dev.to/miku86/nodejs-postgresql-how-to-connect-our-database-to-our-simple-express-server-without-an-orm-10o0) 。

用`node index.js`、
测试您的服务器，它应该运行在 [localhost:8080/users](http://localhost:8080/users) 并显示 PostgreSQL 数据库中的所有(= 1)行。

* * *

## 总结

最后，我们完成了这些步骤:

*   创建了一个快速服务器
*   创建了一个只有一个表和内容的数据库
*   创建了一个读取内容的方法
*   添加了运行此方法的路线

要设置额外的数据库查询，我们只需:

*   用新的 [`sequelize`方法](https://sequelize.readthedocs.io/en/latest/api/model/) ( `create()`、`destroy()`等)在`User.js`中添加新方法。)
*   在`index.js`中添加运行新方法的新路线

* * *

## 下一部分

我们在没有 ORM ( `pg` ) 和有 ORM( `sequelize`)的情况下创建了服务器和数据库[之间的连接。](https://dev.to/miku86/nodejs-postgresql-how-to-connect-our-database-to-our-simple-express-server-without-an-orm-10o0)

在下一部分中，我们将仔细研究这两种方法的优缺点。

* * *

## 进一步阅读

[快递文档](https://expressjs.com/)
[PostgreSQL 文档](https://www.postgresql.org/docs/current/index.html)
[SQL 语法](https://en.wikipedia.org/wiki/SQL_syntax)
[在 npm](https://www.npmjs.com/package/sequelize)
[顺序化文档](https://sequelize.org/master/)