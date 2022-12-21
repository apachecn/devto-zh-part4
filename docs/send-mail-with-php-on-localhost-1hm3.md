# 在本地主机上用 php 发送邮件

> 原文：<https://dev.to/skdbigdev/send-mail-with-php-on-localhost-1hm3>

开发环境

拉雷贡

大家好，

请帮帮我，我正在用 PHP 开发一个网站。我想在本地主机上向我的 gmail 帐户发送电子邮件。

以下是我的 sendmail.ini 文件配置

[发送邮件]

SMTP _ server = SMTP . Gmail . com
SMTP _ port = 587
SMTP _ SSL = auto
error _ log file = error . log
debug _ log file = debug . log
auth _ username =[account@gmail.com](mailto:account@gmail.com)T7】auth _ password = * * * * * * * * * * * * * *
force _ sender =[account@gmail.com](mailto:account@gmail.com)

我在 php.ini 文件中设置了 sendmail 路径

我还禁用了双因素认证，并在我的 gmail 帐户设置中启用了不太安全的应用程序访问

因此

当我发送电子邮件时，它工作了，我在本地化为 C:\ laragon \ bin \ sendmail \ output*的输出目录中收到了格式文本，但不是在我的 gmail 帐户收件箱*中

下面是我在 error.log 文件中得到的错误(19/06/12 15:50:53:必须首先发出 STARTTLS 命令。r3sm21418441wrr.61 - gsmtp)