# gar-cron:提醒您 github 活动的 python 脚本

> 原文：<https://dev.to/prahladyeri/gar-cron-a-python-script-to-remind-you-about-your-github-activity-22ad>

亲爱的乡亲们:

过去几天，我一直在做一个名为 [gar-cron](https://github.com/prahladyeri/gar-cron) 的兼职项目，在这篇文章中，我想与你分享它的细节。

如果您像大多数编码人员一样(至少是对开源充满热情的编码人员)，您希望您的 github 提交活动看起来像这样，不是吗？

[![good committer](img/8389abe2e385f89536850cdf87ac0205.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iimNW0wc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wd78xuxykalh6teo9d1z.png)

但有时，我们会卷入一些个人问题或对编码失去兴趣，这并不好，尤其是如果你的职业依赖于编码的话。编程 90%是一门实用的科学，你练习得越多，在你的行业中就越有优势。

如果你迷失了方向，它就会变成一个循环——你不编码，所以你会沮丧，沮丧会进一步阻止你写更多的代码。这非常类似于在小说作家中很常见的一种叫做*文思枯竭*的现象，毕竟写作和编码有很多相似之处！

这种情况在我身上也发生过几次，在这种时候，我希望有某个守护天使会简单地命令我开始一个项目并开始编码(并一直唠叨，直到我真正开始编码)。

`gar-cron`(或 github 活动提醒 cron)就是试图创造那个守护天使！简单来说，`gar-cron`是一个 python 脚本，它监控你的 github 活动，如果你过去 3 天没有提交任何东西，它会给你发一封电子邮件。它每次运行时都会这样做，所以您可以安排它每天运行一次或两次。

您可以通过运行`crontab -e`，或者通过 windows 机器上的控制面板使用调度程序，将其配置为在 linux 机器上作为 cron 运行。

你还需要一个支持通过 SMTP 发送电子邮件的电子邮件提供商的电子邮件帐户。如果没有 oAuth，像 GMail/Hotmail 这样的流行软件是不支持的，但是你可以在 gmx.com 注册一个。

cron 的安装非常简单:

```
pip install gar-cron 
```

Enter fullscreen mode Exit fullscreen mode

要运行它，您只需:

```
gar-cron 
```

Enter fullscreen mode Exit fullscreen mode

当您第一次运行 gar-cron 时，它会提示您设置 config.json 中的值及其路径。您需要设置的值如下:

```
{
    "github_username": "<your github username>",
    "alert_email": "<your email address>",
    "smtp_server":"<smtp server>",
    "smtp_email": "<smtp sending email>",
    "smtp_username": "<smtp username>",
    "smtp_password": "<smtp password>",
    "smtp_port": "<smtp port>"
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦你这样做了，再次运行`gar-cron`以确保它工作。然后，您可以在 linux 上通过运行`crontab -e`将它配置为用户 cron 作业，或者在 windows 上通过控制面板调度它。

为了测试邮件提醒，你可以在运行它之前，在 config.json 中临时配置一个很久没有提交的人的`github_username`。如果你面临任何问题，你可以在 [github 问题跟踪器](https://github.com/prahladyeri/gar-cron/issues)上提出。

编码快乐！