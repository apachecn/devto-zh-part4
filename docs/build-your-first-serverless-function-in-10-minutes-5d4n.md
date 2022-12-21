# 在 10 分钟内构建您的第一个无服务器功能

> 原文：<https://dev.to/ibmdeveloper/build-your-first-serverless-function-in-10-minutes-5d4n>

我的队友 [Upkar Lidder](https://twitter.com/lidderupk) 发表了一篇[关于如何快速创建你的第一个无服务器功能的伟大教程](https://dev.to/lidderupk/another-simplest-serverless-function-but-seriously-4c7b) ⚡️.我想尝试一下，在这里和你分享这个步骤。无需下载、安装或配置。你可以在 10 分钟内完成本教程⏳。

### 教程步骤

您将完成以下步骤:

1.  创建新的 IBM Cloud 帐户或登录现有帐户
2.  创建新的云功能
3.  测试功能
4.  作为 REST API 调用

走吧！🏃🏻‍♀️🏃🏻‍♂️

### 注册 IBM 云账户

在第一步中，您将注册一个新帐户或登录到一个现有帐户。

[![serverless-helloworld-signin](img/8d094b8924afce58f3fa81a274b444b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uYp845Ys--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katzmax.files.wordpress.com/2019/06/serverless-helloworld-signin.png%3Fw%3D1024%26h%3D448)

1.  [注册一个 IBM 云账户](https://ibm.biz/BdzPSq)或[登录一个现有账户](https://ibm.biz/BdzPSq)
    *   你将获得一个完全免费的账户，可以尝试使用 cloud 40 服务。你可以永远留着它。真不错！
2.  好了

### 创建云函数

在这一步中，您将创建一个新的云函数。

1.  登录后，打开右上角的=菜单，选择**功能** ![serverless-helloworld-menu](img/07c34064f7328b6964940cf03c9cfa82.png)
2.  从 **IBM 云功能**主页面点击**开始创建**按钮![serverless-helloworld-startcreating](img/f8135bca923655423614fb6392cd78d4.png)
3.  从**创建**页面，选择**创建动作**选项![serverless-helloworld-createaction](img/626d4277c0556757170db97ebe5f8849.png)
4.  在**创建动作**页面上
    *   对于**动作名称**,输入*我的中断*
    *   保持**封装包**和**运行时
        ![serverless-helloworld-actionname](img/07fa8d377c459aeaff64eb3b8c7561de.png)** 的默认值
5.  点击**创建**按钮，创建无服务器功能

完成了。您现在应该可以看到功能代码了。

[![serverless-helloworld-code](img/f137aaa35aa875ace8068383a075dac1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yn0Ed6PU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katzmax.files.wordpress.com/2019/06/serverless-helloworld-code.png%3Fw%3D1024%26h%3D452)

### 测试功能

在这一步中，我们将测试函数。

1.  为了使它超级简单，点击**调用**按钮(右上角)。你完了。![serverless-hellowrold-invoke1](img/fb7936f48315ceadd164fc8f160dcc96.png)

现在，为了让它更有趣一点，让我们对函数做一个超级小的改动。

1.  将函数返回的消息更改为 I *在 10 分钟内完成！*
2.  点击**保存**按钮
3.  点击**调用**按钮。您应该会看到更新的消息🤩###作为 REST API 调用

在最后一步中，您将学习如何将函数作为 REST API 调用。

1.  在左侧菜单中点击**端点**
2.  在**网页动作**部分，勾选**启用为网页动作**。点击**保存
    ![serverless-helloworld-webaction](img/3043679c09443927336e526adf1c3cd6.png)**
3.  复制下面的 URL。打开一个新的浏览器窗口，并将 URL 粘贴到地址栏中
4.  您应该会看到函数![serverless-hellowworld-browserrun](img/32bc2d5863f56966b3bce4419642dd65.png)的结果

就是这样。很快，对吧？

### 总结

在这篇博文中，你学习了如何创建你的第一个无服务器函数。如果你想了解更多，涵盖更高级的无服务器主题，请查看我们提供的[无服务器代码模式](https://ibm.biz/BdzPhz)。