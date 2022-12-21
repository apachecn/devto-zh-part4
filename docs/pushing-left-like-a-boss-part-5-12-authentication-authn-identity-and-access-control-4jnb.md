# 像老板一样向左推—第 5.12 部分—认证(AuthN)、身份和访问控制

> 原文：<https://dev.to/azure/pushing-left-like-a-boss-part-5-12-authentication-authn-identity-and-access-control-4jnb>

### 这个系列，还有我的博客，都动了！[来看看](https://wehackpurple.com/g8tg)！

* * *

注意:大部分内容来自 Shruti Kulkarni、Adinath Raveendra Raj、Mennouchi Islam Azeddine 和 Jim Manico 撰写的关于访问控制的 OWASP 备忘单。如果不是，它可能来自令人惊叹的 [OWASP 小抄项目](https://github.com/OWASP/CheatSheetSeries/blob/master/Index.md)的其他产品之一。有关几乎任何 AppSec 主题的更多信息，请查看该项目，它绝对值得您花费时间！

[![B-Sides Vancouver, 2019](img/e93cffb351584e99c163c298236eaf4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZUUhANbY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1jppmvd8wtnowpt01t1a.jpeg)

##### [2019 年 B-Sides 温哥华](https://www.bsidesvancouver.com/)

### 先说一些定义。

**认证**确保使用应用程序的用户是他们声称的真实身份。例如，当我登录我的网络邮箱时，它会验证我就是唯一拥有这个账户的 Tanya Janca。不是一个不同的人，也叫[【坦尼娅·扬卡】](https://twitter.com/shehackspurple)，也不是一个冒充我的人。真实可信的我；拥有帐户的人。

**身份**(数字术语)是一种基于硬件或软件的解决方案，用于证明公民、用户或组织的身份。比如说；为了亲自获得政府当局、银行或其他公司提供的福利或服务，您必须验证您的身份，通常需要使用驾照、护照或其他实体文件。但是，如果您以数字方式(电子方式)验证您的身份，您必须使用基于软件或硬件的解决方案来证明您的身份。

### [在我的新博客上阅读其余内容！](https://wehackpurple.com/29ak)！