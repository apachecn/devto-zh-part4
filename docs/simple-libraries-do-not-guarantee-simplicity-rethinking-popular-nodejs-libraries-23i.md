# 简单的库不能保证简单——重新思考流行的 NodeJs 库

> 原文：<https://dev.to/michi/simple-libraries-do-not-guarantee-simplicity-rethinking-popular-nodejs-libraries-23i>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/simple-libraries-do-not-guarantee-simplicity)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

我经常看到人们围绕着像 expressjs、hapi、koa 等低级路由框架来构建他们的项目。它们经常被标榜为“强大的”,是开发“健壮的”web 和移动应用程序的主要资源。

当然，它开始得相当快，但是很快你将需要控制器、错误处理、访问数据库、ORM、认证、授权、发送邮件、缓存、验证，并且在那一点上最确定的是，一个干净的架构来将所有的东西放在一起。当然，您还遗漏了许多其他东西，比如事件、repl 工具、数据库迁移、ioc 容器、日志记录、csrf 保护以及一种轻松编写测试的方法。

不知不觉中，你就陷入了花费大量时间拼凑自己的框架，而不是编写你首先想要构建的应用程序。我不知道这有什么好的...

[![](img/69b96b5f5bf35fcf5f3918fc74a7d0fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vpaKdZ7n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b238nusnt0n2x4ogy5tt.jpg)

我将在这里列出一些高水平的选择:(如果你知道更多，请告诉我)

*   https://adonisjs.com/(灵感来自拉勒维尔·❤️)
*   [https://nestjs.com/](https://nestjs.com/)
*   [https://sailsjs.com/](https://sailsjs.com/)

虽然最初的学习曲线肯定更高，但从长远来看，你会从中受益。这些框架可以教你很多关于干净的架构和代码，常用的概念，保持你的应用程序安全，以及如何只关注你开始构建的应用程序。

不要误会我的意思，我当然可以看到从零开始构建一切是一个很好的学习经历，对我来说就是这样！但是当我完成了一次并想开始另一个应用程序时，我真的不想再做一遍。在生产中使用定制的身份验证系统时，您也应该非常小心。

另一件事是入职，熟悉高级框架的人可以立即投入编码，而不是花一天时间试图理解项目的架构，只是为了修复一个小错误。如果他们还不知道这个框架，至少有文档和博客文章可以阅读。

最终，我们希望**构建应用**并且**对我们编写的代码有信心**。