# 如何确保您的电子邮件不会被收进垃圾邮件文件夹

> 原文：<https://dev.to/michaelchiche/how-to-make-sure-your-emails-are-not-received-in-the-spam-folder-417i>

为了确保在使用第三方软件(如 intercom、sendgrid 或 sparkpost 或 CRM)时能够正确接收您的电子邮件，不要忘记正确设置您的 DNS，否则您的邮件很可能会被标记为垃圾邮件，而这很可能不是您想要的。

为此，您需要“允许”这些第三方提供商使用您的域名。
否则，想象一下任何服务器将被允许为任何域发送。网络钓鱼将是下一个级别！

您的 DNS 中要使用的参数称为 DKIM(域名密钥识别的邮件),它将告诉所有主要的垃圾邮件检查器可以从其他服务器/域使用该域。

DKIM 实际上主要是一个 TXT 或 CNAME 记录，带有特制的值和主机名

希望这能帮助你直接进入你的用户收件箱！