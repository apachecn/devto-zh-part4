# 如何将 Webapp 部署到 Heroku (Node.js、React.js 和 MongoDB)

> 原文：<https://dev.to/pacheco/how-to-deploy-a-webapp-to-heroku-node-js-react-js-and-mongodb-5a7g>

在我的[上一篇文章](https://dev.to/thisk8brd/my-fullstack-setup-node-js-react-js-and-mongodb-2a4k)中，我解释了如何用 Node.js、React.js 和 MongoDB 创建一个全栈 webapp。
现在我将展示如何将这个应用程序部署到 Heroku。

如果你还没有，可以在这里找到源代码[。
*我推荐按照教程克隆这个回购，但是你可以在你的 on 项目上免费尝试下面的提示。*](https://github.com/thisk8brd/node-react-starter)

克隆项目后，你必须在 Heroku 创建一个账户

> Heroku 是一个云服务器，使用 git 命令，以一种简单易行的方式部署应用程序。这个服务器也有非常好的免费计划，你可以尝试许多类型的应用程序，数据库等等。

创建并登录 heroku 帐户后，您可以创建 Heroku 应用程序。我们可以在命令行中这样做。
导航到您刚刚克隆的项目的根目录，并运行以下命令:

```
$ heroku login
$ heroku create <name of your app> 
```

您应该会得到以下响应:

[![](img/301555955b62deca2029c1d059cda603.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7IhcXcag--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ofjzu66zm8ajcdlly094.png)

现在，在项目根目录下的 package.json 中，在 scripts 下添加下面一行:

```
"heroku-postbuild":  "NPM_CONFIG_PRODUCTION=false npm install --prefix client && npm run build --prefix client" 
```

您的 package.json 文件应该如下所示:

```
{  "name":  "node-react-starter",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1",  "client":  "npm run start --prefix client",  "server":  "nodemon index.js",  "dev":  "concurrently --kill-others-on-fail \"npm run server\"  \"npm run client\"",  "start":  "node index.js",  "heroku-postbuild":  "NPM_CONFIG_PRODUCTION=false npm install --prefix client && npm run build --prefix client"  },  "keywords":  [],  "author":  "",  "license":  "ISC",  "dependencies":  {  "body-parser":  "^1.19.0",  "express":  "^4.17.1",  "mongoose":  "^5.6.3"  },  "devDependencies":  {  "concurrently":  "^4.1.1",  "nodemon":  "^1.19.1"  }  } 
```

现在，保存您的文件并提交您所做的更改。

```
$ git commit -am "Added heroku postbuild scripts" 
```

由于我们已经创建了一个 Heroku 应用程序，我们可以将它推送到服务器。

```
$ git push heroku master 
```

您应该会看到一些构建脚本正在运行，最后会显示一条消息，说明您的应用程序已成功部署到 Heroku。

现在，您可以导航到您的 [Heroku 仪表板](https://dashboard.heroku.com/apps)并看到您的应用程序已成功部署。

但是这个应用还没有准备好，我们还需要添加数据库。

[![](img/faa9b96f459d53870ae832a8919833d9.png)](https://i.giphy.com/media/cMVgEhDeKzPwI/giphy.gif)

点击进入你在 Heroku 的项目。您应该会看到类似这样的内容:

[![](img/e1b3bc9e0b897bbca52ff922d4c6dd61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tV59Ebk5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xx98adpj02cdquacwv75.png)

*   单击配置附加组件。
*   搜索 mLab MongoDB。
*   单击它并选择选项“无沙盒”

*如果您导航到 Settings 并单击 Reveal vars 选项，您会看到它自动添加了 MongoDB 环境变量。*

现在，您的项目应该已经启动并运行，您可以通过点击 Heroku 仪表板页面顶部的“打开应用程序”按钮来查看。

瞧啊。我们成功部署了一个生产就绪的全栈项目。

[![](img/8b0c89bfdda22d163117dc40375f2aa3.png)](https://i.giphy.com/media/CiTLZWskt7Fu/giphy.gif)

希望这篇教程对你有用。