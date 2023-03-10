# 使用 Sequel Pro 通过 JawsDB 设置 Heroku 站点

> 原文：<https://dev.to/laurengranada/setting-up-your-heroku-site-with-jawsdb-using-sequel-pro-5fe6>

我在网上到处找到一些帖子，帮助修复了一个或另一个错误，但并没有完全指导我完成这个过程。我的日志中有这样的错误:

[![](img/537839ff81003a493454d15ba31e1b22.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yXfuUF-1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4kiza9r6e7zrtze40ynb.png)

希望这有所帮助！

首先:把你所有的代码放到你的 github repo 里。一旦输入:

```
heroku create 
```

在命令行中创建 heroku 连接。在你的浏览器中登录 heroku，进入你的新应用页面。

2-单击“资源”选项卡，找到“附加组件”下的区域。

[![](img/7d6d2f022a640089d1a85e39a40816db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OYQF0kym--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rp4ss7uiuomckub4qjh1.png)

3-开始在输入框中键入“JawsDB ”,结果列表应该如下所示:

[![](img/f4b143e9981b87b65622cb81f37c1c16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jNXc8MmL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rlhzcatgpgm4ngfadppi.png)

选择“JawsDB MySQL”连接到您的应用程序。将显示一个弹出窗口，为您提供计划选项。选择“Kitefin”(免费版)，然后单击“Provision”

[![](img/3b568a0268f45c51f80c5c033d241819.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uJ5f3bfz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/39jqemglbgn144au6bby.png)

成功添加后，它将列在您的资源页面上。继续点击它。这将引导您在一个单独的选项卡中进入 JawsDB 的设置页面。

[![](img/4524d0d8f1fcdaefb5ce401fac04f252.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NEKT2iRi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3j97vo1ou54ti36x8o12.png)

[![](img/80e5a223f5d12916c76ef0fe71128a51.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9vyYPN7M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4g3l80nrhe5oyn3mjse0.png)

4-让我们进入 Sequel Pro。您需要创建一个新的连接。确保您正在创建一个“标准”连接。在这一步中，您需要将 JawsDB 设置(上图)*中的“主机”、“用户名”和“密码”值准确地复制并粘贴到 Sequel Pro 连接值(下图)中，然后单击“连接”。*

[![](img/c29f338baad70b8a289d912d03bac84c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p8b-OtK_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iy8njmlhnkglwou7v9f5.png)

5-在“选择数据库”下拉菜单中，选择随机字母下列出的数据库。

[![](img/e92f67e69b4fadc5a145f2bf50454ff4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ysUhmO7j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i7euslhbgxr4duhfghgd.png)

在“查询”选项卡中，创建您的模式。确保 id、createdAt 和 PRIMARY KEY 如下所示，并运行它。

[![](img/8c2f47c90d66d2883b4a5bc1026e535f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5ZTqv1Tt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i4nh9vhqinhtou3bxple.png)

6-在“结构”选项卡下，编辑“默认”值以显示“当前时间戳”。从下拉菜单中编辑“额外”值，以显示“在更新当前时间戳时”。(下图)

[![](img/f9a0e3ddce6ea8cd971986298ff7dd21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qQBbKBBK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cz5fqnjwbrrazrkrced3.png)

7-让我们进入你的“connection.js”文件，稍微修改一下。确保在这一部分包括您的“端口”。无意中遗漏了这一点是我犯一些错误的原因。

```
// Set up MySQL connection.
var mysql = require("mysql");
var connection;
//make connection
if (process.env.JAWSDB_URL){
  connection = mysql.createConnection(process.env.JAWSDB_URL);
}else{
  connection = mysql.createConnection({
    port: 3306,
    host: "localhost",
    user: "root",
    password: "",
    database: "burgers_db"
  })
}
connection.connect();
// Export connection for our ORM to use.
module.exports = connection; 
```

8-让我们进入你的‘server . js’文件并改变端口变量。添加“process.env.PORT”允许访问数据库。

```
var port = process.env.PORT || 3000; 
```

9-确保将您的所有更改推送到您的 github repo。一旦成功并完成，推进到 heroku。

```
git push heroku master 
```

这应该可以完成所有的事情，并将你的 mySQL 数据库连接到你的 heroku 应用程序。如果您遇到任何错误，请务必查看您的日志。

一个活生生的例子，你可以看看我的项目 github 回购:[https://github.com/laurengranada/week-14-burger](https://github.com/laurengranada/week-14-burger)

祝你好运！