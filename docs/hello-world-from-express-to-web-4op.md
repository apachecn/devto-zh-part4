# 你好，世界！-从快递到网络

> 原文：<https://dev.to/pofoda/hello-world-from-express-to-web-4op>

你好，世界！是常见的入门教程，几乎每个 dev 都经历过。

在本教程中，我将向你展示一个用“NodeJS”设置“Express”的简单方法

为什么是“Express”和“NodeJS”？NodeJs 是我们以轻量级方式(i/o)处理服务器端代码的方式。
Express 是 NodeJS 的一个框架，使开发网站、web 应用和 API 变得更加容易。

1.  假设你已经有了你最喜欢的编辑器，我们可以开始了，如果你不知道任何…‘vs code’(visual studio 代码)是一个不错的选择
2.  你还需要安装 NodeJS(软件)。([https://nodejs.org/en/download/](https://nodejs.org/en/download/)
3.  假设你一直跟着做，你现在必须在你的编辑器中打开终端，输入“npm init”，它会问你几个问题，这取决于你的系统，你应该可以在大多数问题上点击回车键，“入口点:”可以重命名为“app.js”。
4.  现在是在服务器上获取 Express 的时候了，只需再次打开你的终端，再次使用‘NPM’即可。键入:“npm install Express - save”，如果一切顺利，它应该在根目录下的“package.json”文件中。
5.  现在东西都安装好了，我们可以开始使用 Express 了，你的“入口点”应该在根目录下有一个同名的文件。fx 在第三步我建议它为“app.js ”,如果你这样命名的话。
6.  进入你的“入口点”,准备写一点代码。

```
// Loads express as server program
const express = require('express');
// creates an express application
const app = express();

// makes you able to create ‘routes’ in an separated filed called: “routes.js”
require('./server/routes/routes.js')(app);

// Allows server to handle the ‘public’ folder, by serving HTML, CSS, JavaScript and pictures
app.use(express.static('public'));

// starts server on port 3000 and gives you a link in the terminal with some visual presentation
const port = 3000;
app.listen(port, (error) => {
if (error) console.log(error);
         console.log('\x1b[35m%s\x1b[0m', '================================================================'); console.log('Server is listening on port %s, address: %s', port, 'http://localhost:' + port);
}); 
```

1.  现在你只需要这个结构，就可以开始了。结构:

```
· “root”
· Public
·   Img
·   CSS
·     style.css
· Server
·   routes
·     routes.js
· license
· Package-lock.json
· Package.json
· app.js 
```

EJS 视图引擎(奖金)
EJS 是一个简单的模板语言，让你用普通的 JavaScript 生成 HTML 标记。

我们以前做过，我们将再次这样做，要安装这个包，我们需要打开终端并写入:“npm install ejs - save”，然后它应该准备好工作。
我们所要做的就是允许服务器使用我们的新包:
找到进入 app.js 的方法，就在我们允许“routes”的部分上面，在另一个文件中你应该放入:

```
// set view-engine as ejs 
app.set('view engine', 'ejs');
// point at the folder/file where the views are placed
app.set('views', './server/views'); 
```

app.js 现在应该是这样的:

```
// Loads express as server program
const express = require('express');
// creates an express application
const app = express();
// sæt viewengine til ejs 
app.set('view engine', 'ejs');
// peg på den mappe hvor alle views filerne er placeret
app.set('views', './server/views');

// makes you able to create ‘routes’ in an separated filed called: “routes.js”
require('./server/routes/routes.js')(app);

// Allows server to handle the ‘public’ folder, by serving HTML, CSS, JavaScript and pictures
app.use(express.static('public'));

// starts server on port 3000 and gives you a link in the terminal with some visual presentation
const port = 3000;
app.listen(port, (error) => {
if (error) console.log(error);
         console.log('\x1b[35m%s\x1b[0m', '================================================================'); console.log('Server is listening on port %s, address: %s', port, 'http://localhost:' + port);
}); 
```

MySQL 数据库(奖金)
MySQL 是一个基于 SQL 的数据库，很容易管理(在我看来)。
如果你是 MySQL 的新手，你应该检查一下:“PHPMyAdmin”以方便和直观地表示
首先，像上次一样进入终端，键入:“npm install mysql2 - save”，如果一切正常，你可以开始在 mysql2 上工作了。

1.  对结构做一点改变，在“服务器”下新建一个文件夹，命名为“配置”，在那里你应该新建一个名为“mysql.js”的文件。新结构:

```
· “root”
· Public (everything here is accessible for anyone with connection)
·   Img
·   CSS
·     style.css
· Server
·   config
·     mysql.js
· routes
·   routes.js
· license
· Package-lock.json
· Package.json
· app.js 
```

1.  现在转到最新添加的文件‘MySQL . js’并输入代码:

```
// allows the server to load and run ‘mysql2’
const MySQL = require('mysql2/promise'); //

// module.exports allow other files in the document to access the code and simply make a connection to the chosen database
module.exports = {
  connect: async function () {
     return await mysql.createConnection({
        host: 'localhost',
        user: 'root',
        password: '', // not important
        port: '3000',
        database: 'the_name_database' // whatever name you gave your database
     });
  }
} 
```