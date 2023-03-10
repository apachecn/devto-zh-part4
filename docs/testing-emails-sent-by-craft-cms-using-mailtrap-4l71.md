# 使用邮件陷阱测试 Craft CMS 发送的电子邮件

> 原文：<https://dev.to/piotrpog/testing-emails-sent-by-craft-cms-using-mailtrap-4l71>

这篇文章最初发表在[craftsnippets.com](http://craftsnippets.com?s=dt)上。去那里寻找更多与工艺相关的文章，并准备好使用模板组件。

## 关于邮件陷阱

[Mailtrap](https://mailtrap.io/) 是假的 SMTP 服务器，可以用来**测试和调试**你的网站发送的所有电子邮件。不管收件人地址是什么，Mailtrap 服务器实际上并不发送任何东西——它只是存储所有的邮件，以便以后查看和分析。

使用 Craft CMS [应用程序配置文件](https://docs.craftcms.com/v3/config/app.html)我们可以覆盖使用本地 **Sendmail** 程序的默认邮件适配器，并连接到外部 SMTP 服务器，如 **Mailtrap** 。

## 获取邮件陷阱凭证

首先，您需要获得将用于连接到服务器的邮件陷阱凭据。

在 Mailtrap 网站注册并登录。你会看到你的收件箱列表，现在只有一个。点击右边的齿轮图标，从“SMTP 设置”选项卡复制凭证。

mailtrap 博客上的[这篇文章提供了详细的说明和截图。](https://blog.mailtrap.io/2018/09/18/mailtrap-getting-started-guide/#first-steps-with-the-basic-mailtrap-functionality)

## 覆盖默认邮件适配器

Craft CMS 提供了三种邮件适配器:

*   Sendmail -它使用服务器 Sendmail 功能。这是默认适配器。
*   SMTP -可用于连接外部 SMTP 服务器。
*   Gmail -可用于连接 Gmail 服务器。

还有像 [Mailgun](https://plugins.craftcms.com/mailgun) 这样自己添加适配器的插件。为了连接到 Mailtrap，我们需要将 **Sendmail** 邮件适配器切换到 **SMTP** 适配器。

首先，让我们在`.env`文件中定义我们的凭证。那是你应该保存所有密码和敏感数据的地方。

```
SMTP_HOST=smtp.mailtrap.io
SMTP_PORT=2525
SMTP_USERNAME=your_username
SMTP_PASSWORD=your_password 
```

不要忘记将用户名和密码改为从 mailtrap 获得的。

现在，打开`config/app.php`文件。如果您之前没有修改它，它应该包含到示例模块的链接——它可以被安全地删除。

您的邮件程序配置应该如下所示:

```
<?php
return [
    'components' => [
        'mailer' => function() {
            $settings = \craft\helpers\App::mailSettings();
            $settings->transportType = \craft\mail\transportadapters\Smtp::class;
            $settings->transportSettings = [
                'useAuthentication' => true,
                'host' => getenv('SMTP_HOST'),
                'port' => getenv('SMTP_PORT'),
                'username' => getenv('SMTP_USERNAME'),
                'password' => getenv('SMTP_PASSWORD'),
            ];
            $config = \craft\helpers\App::mailerConfig($settings);
            return Craft::createObject($config);
        },
    ],
]; 
```

如你所见，我们重写了 Craft CMS 应用程序的`mailer`组件。

现在，Craft CMS 发送的每封电子邮件都会被**重定向到 Mailtrap** 。它还考虑了插件发送的电子邮件。您可以使用“忘记密码”功能快速测试一切是否正常。

一旦你检查了收件箱，你就可以开始检查你的邮件了。你可以查看他们的预览，HTML 代码，原始数据，分析他们的潜在问题。记下你发送了多少封电子邮件——免费的 Mailtrap 账户每月限制 500 封邮件，但是花几块钱你就可以大大提高这个数字。

## 进一步阅读

*   [邮件陷阱入门指南](https://blog.mailtrap.io/2018/09/18/mailtrap-getting-started-guide)
*   [制作 CMS 文档-应用配置文件](https://docs.craftcms.com/v3/config/app.htm)