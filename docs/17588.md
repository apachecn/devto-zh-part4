# 节点 JS 中的 REST API

> 原文：<https://dev.to/demg_dev/rest-api-in-nodejs-d5m>

嗯！！这是我在这里的第一篇文章，现在我将向你展示如何用简单的步骤在 NodeJS 中创建一个 API REST，我们需要大约 30 分钟或更少的时间来创建，如果你有一些关于 javascript 的知识，理解所有代码会变得很容易。

> 开始吧！！！
> **注意:**我警告你，我是 NodeJS 的初学者，我与你分享这个是因为它是进入 de API REST 和 NodeJS 的好入口。

### 创建项目

首先，我们需要创建 NodeJS 项目，因此您开始创建一个文件夹，然后在终端或 cmd 中执行以下代码:

```
npm init 
```

在 NodeJS 完成所有配置步骤后，接下来是安装我们需要的所有依赖项。

```
npm install body-parser --save
npm install cors --save
npm install express --save
npm install mysql --save 
```

“CORS”依赖也许你不需要安装，但如果你想避免一些通信错误，我建议你安装。

“MySQL”依赖关系，您可以为您的首选数据库进行更改。

### 文件

我们需要创建一个名为' **server.js** 的文件，并将'**package . JSON**main 属性更改为这个文件名，因为这里设置为' *index.js* '。

### 依赖关系

我们需要添加以下依赖项来使 work 成为我们的 API。

> **注**:我使用 MySQL 作为数据库提供者。
> **注 2** :我创建了一个 API，对 MySQL 服务器中的‘saki la’数据库进行 CRUD 函数。

```
var express = require('express');
var app = express();
var bodyParser = require('body-parser');
var mysql = require('mysql');
const cors = require('cors'); 
```

在这个依赖项中，我添加了“cors”依赖项，因为当我尝试从 Angular 使用时，我在连接到 API 时遇到了一些问题。

### 初始化

这里我们需要初始化一些依赖项，我将在 API 的后面使用它们。

> **注意:**我认为你对 javascript 有一些了解，所以我避开了代码中明显的部分，解释了重要的东西。

```
app.use(bodyParser.json());
app.use(cors());
app.use(bodyParser.urlencoded({
    extended: true
})); 
```

### API 函数

现在将添加所有的 API 函数，在这一点上用户和 API 将可以相互通信。

#### 默认路线

```
// default route
app.get('/', function(req, res) {
    return res.send({ error: true, message: 'hello' });
}); 
```

对此需要解释的一件事是，我在所有响应中返回了一个标准化的 JSON 对象，因为我认为我对用户获得的数据有更多的控制权。

> **注:**的 JSON 是:
> 
> `{ error: true, data: data, [value1:value1,...] }`

如果你需要的话，这个默认路径可能会变成令牌初始化或者一些消息来检查用户是否正确地连接并从 API 获取数据。

#### MySQL 配置

在这里你可以改变你的首选数据库。

```
// connection configurations
var dbConn = mysql.createConnection({
    host: 'localhost',
    user: 'admin',
    password: 'root',
    database: 'sakila'
});

// connect to database
dbConn.connect(); 
```

在 createConnection 中，只需添加数据库的登录信息，然后打开连接。

> **注:**我用的数据库是 MySQL 服务器中安装的示例数据中的 sakila，我工作的表是 staff。

#### 获取用户

我用 API 中的第一个入口点在我为此 API 创建的 Angular 应用程序中进行简单的登录，不需要您添加，或者您可能有更好的想法来进行用户登录，*在评论中提出您对此部分的解决方案*。

```
// Retrieve user with username 
app.get('/user/:username&:password', function(req, res) {
    const username = req.params.username;
    const password = req.params.password;
    if (!username) {
        return res.status(400).send({ error: true, message: 'Please provide username' });
    }
    dbConn.query('SELECT * FROM sakila.staff WHERE username="' + username + '" AND password="' + password + '";', function(error, results, fields) {
        if (error) throw error;
        if (results.length > 0) {
            return res.send({ error: false, data: results[0], username, password });
        } else {
            return res.send({ error: true, data: results[0], username, password });
        }
    });
}); 
```

在这段代码中，我使用“req.params.YOUR_PARAM”来获得 API 调用中的参数 send，就像你看到的那样，很容易获得数据，重要的一点是，如果你做 POST，你可以用 JSON 格式发送。

如你所见，这只是一个 GET 请求，可能不安全，我会使用 POST，因为我发送了一个私有数据，但这是为了学习，我想告诉你如何在 GET 请求中传递一些参数。

就像你看到的那样，数据库的连接就像查询字符串和设置我需要的数据一样简单。

#### 获取所有用户

现在我们添加入口点来获取数据库中的所有用户，检查下面的代码:

```
// Retrieve all users 
app.get('/users/', function(req, res) {
    dbConn.query('SELECT * FROM sakila.staff', function(error, results, fields) {
        if (error) throw error;
        if (results.length > 0) {
            return res.send({ error: false, data: results });
        } else {
            return res.send({ error: true, data: results });
        }
    });
}); 
```

如您所见，这是一个从所有表和所有数据中进行的简单选择。

#### 按 Id 获取用户

这一部分是为了防止您只需要一个用户而不是所有用户。

```
// Retrieve user with id 
app.get('/user/:staff_id', function(req, res) {

    let user_id = req.params.staff_id;

    if (!user_id) {
        return res.status(400).send({ error: true, message: 'Please provide user_id' });
    }

    dbConn.query('SELECT * FROM sakila.staff where staff_id=?', user_id, function(error, results, fields) {
        if (error) throw error;
        if (results.length > 0) {
            return res.send({ error: false, data: results[0], user_id });
        } else {
            return res.send({ error: true, data: results[0], user_id });
        }
    });
}); 
```

就这么简单，我用 id 得到一个用户。

#### 添加用户

这里开始变魔术，因为前面的代码只是为了获取数据，但现在我们要添加一个用户。

这里使用 HTTP 调用中的 POST 操作来更安全地发送私有数据。

> **注意:**为了更标准化和更易维护的 API，我将接收一个带有用户信息的 JSON 对象，然后将该对象直接插入数据库。

```
// Add a new user  
app.post('/add', function(req, res) {
    let user = req.body;
    console.log("add user");

    if (!user) {
        return res.status(400).send({ error: true, message: 'Please provide user' });
    }

    dbConn.query("INSERT INTO sakila.staff SET ? ", user, function(error, results, fields) {
        if (error) throw error;
        return res.send({ error: false, data: results, message: 'New user has been created successfully.' });
    });
}); 
```

就像你在这部分看到的，最难理解的是插入，因为我们将完整的对象添加到 de 数据库中，但是就像你看到的那样工作正常，并且更容易维护代码和解决问题。

> **注意:**在这一部分中，我不能检查其他数据库，如果像 MySQL 发送一个完整的对象。

#### 更新用户

正如您在前面的代码中看到的，这可能是相同的事情，但是我们在 HTTP 调用中使用了 PUT 操作，而不是 POST 操作。

```
//  Update user with id
app.put('/update', function(req, res) {
    let user = req.body;

    if (!user.staff_id || !user) {
        return res.status(400).send({ error: user, message: 'Please provide user and user_id' });
    }

    dbConn.query("UPDATE sakila.staff SET ? WHERE staff_id = ?", [user, user.staff_id],
        function(error, results, fields) {
            if (error) throw error;
            return res.send({ error: false, data: results, message: 'user has been updated successfully.' });
        });
}); 
```

#### 删除用户

现在添加代码，以便在 HTTP 调用中使用 delete 操作删除用户。

```
//  Delete user
app.delete('/delete/:staff_id', function(req, res) {

    let user_id = req.params.staff_id;

    if (!user_id) {
        return res.status(400).send({ error: true, message: 'Please provide user_id' });
    }
    dbConn.query('DELETE FROM sakila.staff WHERE staff_id = ?', [user_id], function(error, results, fields) {
        if (error) throw error;
        return res.send({ error: false, data: results, message: 'User has been updated successfully.' });
    });
}); 
```

现在所有的 CRUD 操作都完成了。

### 整理文件

现在要完成我们的 API 服务器文件只需要在文件末尾添加以下代码:

```
// set port
app.listen(3000, function() {
    console.log('Node app is running on port 3000');
});

module.exports = app; 
```

在这一部分中，只需在控制台中添加端口和一些日志，就可以注意到一切都正常工作。

至此，所有的 API 都已完成，现在您可以运行下面的代码:

```
npm start 
```

现在你可以使用你自己的网页或者其他软件来调用 API，你会看到所有的数据都在运行，并且你的数据库中的数据都被修改了。

## 结论

现在你有了一个完整的 API REST 功能，可以在你的所有项目中使用，现在你的家庭作业是使用令牌认证来保护 API，并根据你的需要做一些改进或修改代码。

我很高兴在这里发表我的第一个帖子，并开始我的梦想，帮助人们进入这条美妙的道路。