# 密码管理器有更好的替代品

> 原文：<https://dev.to/simplelogin/there-are-better-alternatives-to-password-manager-1655>

在不同的网站上重复使用同一个密码在安全性方面是很糟糕的。就像黑客入侵了一个网站的数据库一样，他/她也可以进入你的其他账户。

但是为每个网站生成不同的密码并记住它们对人类来说是不可能的😅。这就是为什么**密码管理器**像 [LastPass](https://www.lastpass.com) 、 [Dashlane](https://www.dashlane.com) 、 [1Password](https://1password.com) 等被创造出来。他们的原则很简单:有一个**主**密码，允许你管理所有其他密码(那么我们应该把其他的*从密码*吗😜？).由于失去这个主密码将打开我们所有秘密的端口，每个密码管理员都有自己的[多因素认证](https://en.wikipedia.org/wiki/Multi-factor_authentication) (MFA)来保护它，从[一次性密码](https://en.wikipedia.org/wiki/Time-based_One-time_Password_algorithm) (TOTP)到打印一个你一直带在身边的密钥。

这些工具越来越受欢迎，因为用户越来越关心隐私，但这些用户仍然是少数。原因之一是因为困难和不寻常的设置过程，特别是在手机上。但好消息是，现在谷歌和苹果已经在 [Chrome](https://passwords.google.com/intro) 和 iPhone/Mac 中集成了自己的密码管理器，使得密码管理器的概念更容易被普通大众接受。

一切都好，对吗？不完全是因为密码管理器只是安全问题解决方案的一半。让我解释一下。

登录通常有两个部分:用户名/电子邮件和密码。密码管理器只保护密码，不保护电子邮件。与密码相比，丢失电子邮件的灾难性影响较小，但如果泄露，可能会导致以下后果:

*   未经请求的电子邮件，也称为垃圾邮件
*   社交黑客:知道你在一些网站上，会为复杂的社交黑客提供足够的信息。

* * *

那么什么是*真正的*解呢？对我来说，隐私和安全的解决方案是在网上拥有不同的**人物角色**:一个用于专业工作(例如 Linkedin)，一个用于朋友&家庭(脸书)，一个用于自拍(Instagram😄)，一个为了激情(旅游，足球等)。这些角色是完全独立的，知道其中一个不会泄露其他的。这个理想世界的第一步是为每个网站设置不同的电子邮件**和**密码。

苹果明白这一点，并在今年早些时候发布了用苹果按钮登录的[。](https://developer.apple.com/sign-in-with-apple/) [SimpleLogin](https://simplelogin.io) 也从电子邮件开始应对这一挑战:用户可以创建随机的电子邮件别名来保护他们真实的个人电子邮件。但电子邮件只是第一步，接下来是其他个人信息，如年龄、性别、电话号码、地址等。(声明:我恰好是 SimpleLogin 联合创始人。)

这些 SSO 按钮也没有设置:没有更多额外的应用程序安装在手机上，主密码通常已经由浏览器或操作系统直接处理。

但是现在的挑战是采用。如果开发人员不采用这些替代方案并坚持使用传统的用户名/密码，用户仍然需要创建自己的密码或使用密码管理器。因此，确保通过实现这些[社交登录](https://en.wikipedia.org/wiki/Social_login)按钮之一来缓解用户的生活🙏。

请让用户名/密码安息在⚰️.

* * *

以下是一些在不同框架/语言中添加社交登录按钮的教程:

*   [烧瓶/蟒蛇](https://dev.to/simplelogin/create-a-flask-application-with-sso-login-f9m)
*   [Django/Python](https://simpleisbetterthancomplex.com/tutorial/2016/10/24/how-to-add-social-login-to-django.html)
*   [节点 j](https://docs.simplelogin.io/docs/passport/)
*   [香草 JS](https://docs.simplelogin.io/docs/frontend-js/)