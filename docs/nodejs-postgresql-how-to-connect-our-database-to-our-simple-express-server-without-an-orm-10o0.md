# NodeJS & PostgreSQL:如何将我们的数据库连接到我们的 Simple Express 服务器(没有 ORM)

> 原文：<https://dev.to/miku86/nodejs-postgresql-how-to-connect-our-database-to-our-simple-express-server-without-an-orm-10o0>

## [T1】简介](#intro)

我们了解到:

*   [如何使用 PostgreSQL 做事情](https://dev.to/miku86/postgresql-what-is-it-how-to-install-it-16hk)
*   [如何用 express 创建服务器](https://dev.to/miku86/nodejs-how-to-create-a-simple-server-using-express-1n9d)

现在我们想学习如何用`pg`和 [no ORM](https://stackoverflow.com/q/1279613) 连接 PostgreSQL 数据库和我们的 express 服务器。

* * *

## 创建项目文件夹，做`npm init`，安装快递& pg

```
mkdir node-postgres-pg
cd node-postgres-pg
npm init
npm i express pg 
```

Enter fullscreen mode Exit fullscreen mode

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
createdb -O postgres node-postgres-pg
psql -d node-postgres-pg
\conninfo
CREATE TABLE users(id SERIAL PRIMARY KEY, nickname TEXT NOT NULL);
INSERT INTO users(nickname) VALUES ('miku86'); 
```

Enter fullscreen mode Exit fullscreen mode

用`SELECT * FROM users;`、
测试你的数据库，应该还有`1 row`。

* * *

## 用通用查询创建`database.js`

```
const { Pool } = require('pg');
// your credentials
DATABASE_URL = 'postgres://[db-user]:[password]@127.0.0.1:5432/node-postgres-pg';

const pool = new Pool({
  connectionString: DATABASE_URL
});

// a generic query, that executes all queries you send to it
function query(text) {
  return new Promise((resolve, reject) => {
    pool
      .query(text)
      .then((res) => {
        resolve(res);
      })
      .catch((err) => {
        reject(err);
      });
  });
}

module.exports = {
  query
}; 
```

Enter fullscreen mode Exit fullscreen mode

注意:这个实现非常简单，不用于生产。您不应该将您的凭证移动到这个文件中，您应该使用类似于`dotenv`的东西。您还应该将数据库配置移到一个单独的文件中。但是为了简单起见，我尽可能地简化这个例子。

* * *

## 用一种方法创建`User.js`来读取所有数据库行

```
const database = require('./database');

const User = {
  async readAll(req, res) {
    try {
      const readAllQuery = 'SELECT * FROM users';
      const { rows } = await database.query(readAllQuery);
      return res.send({ rows });
    } catch (error) {
      return res.send(error);
    }
  }
};

module.exports = User; 
```

Enter fullscreen mode Exit fullscreen mode

一个用户对象，有一个方法`readAll()`。
这个方法发送一个 PostgreSQL 查询(`SELECT * FROM users`)到`database.js`(我们在顶部导入了它)中的通用查询，并等待响应，在那里它析构`rows`并返回它们。

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

*   用新的查询(`INSERT`、`DELETE`等)在`User.js`中添加新的方法。)
*   在`index.js`中添加运行新方法的新路线

* * *

## 下一部分

我们将学习如何使用表单。

* * *

## 进一步阅读

[快递文档](https://expressjs.com/)
[PostgreSQL 文档](https://www.postgresql.org/docs/current/index.html)
[SQL 语法](https://en.wikipedia.org/wiki/SQL_syntax)
[pg at NPM](https://www.npmjs.com/package/pg)
[pg 文档](https://node-postgres.com/)