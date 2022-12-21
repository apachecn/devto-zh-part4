# 为什么你不应该创建自己的认证系统

> 原文：<https://dev.to/simplelogin/why-you-shouldn-t-create-your-own-authentication-system-hjg>

正确实施**认证系统是**硬**。**

 **如果您现在开始创建您的网站/应用程序，请使用**第三方认证**。第三方认证可以是**社交登录**(如无处不在的`Login with Facebook`按钮)或**身份管理**工具，如 [Auth0](https://auth0.com) 或 [Okta](https://www.okta.com) 。

这不仅仅是在数据库中存储用户名/密码。在标准的 web 应用程序中，身份验证系统需要:

*   以明文形式存储密码[哈希](https://en.wikipedia.org/wiki/Cryptographic_hash_function#Password_verification)而不是密码。而且每个用户都应该有自己的[盐](https://en.wikipedia.org/wiki/Salt_(cryptography))。并且散列函数应该是[计算起来很昂贵的](https://codahale.com/how-to-safely-store-a-password/)。对于所有这些，你需要对彩虹表、Bcrypt、散列算法和 salts 有很好的理解。
*   有电子邮件验证和重置密码功能。为此，您可能需要生成并存储一个临时的、短期的令牌，用于激活帐户或重置密码。这是简单的部分。确保你的电子邮件不落入**垃圾邮件**文件夹要难得多。

在前端，应用程序需要有一个像样的用户界面:一个至少验证电子邮件和密码的登录表单，一个注册表单，一个重置密码表单。更不用说现代认证期望支持 [MFA](https://en.wikipedia.org/wiki/Multi-factor_authentication) 以及很快(希望如此) [WebAuthn](https://en.wikipedia.org/wiki/WebAuthn) 。

更重要的是，您的用户不希望经历冗长的注册过程并创建另一个用户名/密码。如果没有一个合适的*密码管理器*(可能 99%的用户不使用它)，他们倾向于重复使用同一个密码，这在安全性方面是不好的！

那么，为什么要选择这条艰难的道路，并面临密码泄露、数据库被黑以及最重要的劣质用户体验的风险呢？当然，一些应用程序需要有自己的认证系统(银行应用程序就是一个例子，但这也随着 [PSD2](https://en.wikipedia.org/wiki/Payment_Services_Directive) 而改变)，但它们只是少数。

* * *

**那么，我的应用应该使用哪种第三方验证呢？**

这个真的要看你的 app 了。如果获得用户的脸书订阅可以给你的应用带来很大的好处，那么你可能应该用脸书的登录**。如果访问用户的 Twitter feed 是有价值的，那么用 Twitter** 登录**更合适。如果你的应用程序只需要用户的基本信息，如电子邮件、姓名、头像图片，那么我会建议[用 SimpleLogin](https://simplelogin.io) 登录，这是一个开发者友好的登录解决方案。声明:我碰巧是 SimpleLogin 的联合创始人，所以这个建议显然是主观的😅。**

我们都应该停止要求用户选择用户名和密码。有更好的选择。

请让用户名/密码安息在⚰️.**