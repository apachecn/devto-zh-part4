# 如何用 Nodemailer 和 Namecheap 发送神奇链接

> 原文：<https://dev.to/toomaime/how-to-send-a-magic-link-with-nodemailer-and-namecheap-b3e>

在我的项目 [Growthhacklist](https://nodemailer.com/about/) 中，我需要一种简单快捷的方式，在我的登录页面上留下新用户的电子邮件地址后，给他们发送一个“神奇链接”。为此，我用[节点邮件](https://nodemailer.com/about/)和 Namecheap 拼凑了一个小配方。

[![](img/a616f97967d288b7395ed8a1851a7682.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--47V2psrN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xbttcjyskhjhhdym0hwk.png)

正如你在我的登录页面上看到的，我有一个小输入框，用户可以在那里提交电子邮件。点击“开始学习”按钮后，他将被重定向到“创建帐户”页面。

现在在后台我的小食谱将开始工作。为了让“神奇链接”工作，你需要一个廉价的帐户(在我的情况下，也可以是另一个电子邮件提供商)和 npm 包 Nodemailer。

首先安装 npm 包，并要求它在您的控制器中(我使用的是 express js ),带有

*const node mailer = require(' node mailer ')；*

在那之后，你需要下面的代码，它将发挥神奇的作用。

[![](img/87b2dd331c1583c4bd71587c13eb7f9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QnHQXPEo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5t743emqy499qdoynip5.png)

var transporter 部分是为您的电子邮件提供商准备的。出于安全原因，请确保将帐户详细信息放在环境变量中。

在 email 选项中，您需要设置发送电子邮件的地址。然后，您可以添加您的电子邮件地址的主题，并在 html:设置您的第一封电子邮件的欢迎信息给用户。

您还需要在某个地方添加 post 请求。在我的例子中，我将它添加到我的 index.js 文件中。

*router.post('/email '，user controller . new email)；*

您也可以在一个文件中这样做，但是在 routes 文件夹中放一个 index.js 会更干净。现在您唯一需要做的事情就是将表单代码添加到您的前端。我正在使用 Pug JS，但当然你也可以使用其他模板引擎。

**前端代码**
就这样了。我希望这有所帮助，如果你有问题，请在评论区告诉我。

[![](img/39a5a4a12fff7046c34249f712a21382.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FWuHEt9e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aiyfpwu94x21q8otjtmu.png)

已经这样了。我希望这有所帮助，如果你有问题，请在评论区告诉我。

这篇文章最初出现在我的项目 [Growthhacklist](https://growthhacklist.com/methods/send-a-magic-link-with-nodemailer-and-namecheap) 上