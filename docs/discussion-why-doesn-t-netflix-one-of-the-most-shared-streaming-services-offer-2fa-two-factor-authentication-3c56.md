# 讨论:为什么共享最多的流媒体服务之一网飞不能提供 2FA(双因素认证)？

> 原文：<https://dev.to/dietertroy/discussion-why-doesn-t-netflix-one-of-the-most-shared-streaming-services-offer-2fa-two-factor-authentication-3c56>

网飞是世界上最大的流媒体服务之一，每年拥有数百万用户。这篇文章不涉及内容或用户，而是提出了一个很好的问题。

网飞:为什么登录过程没有 2FA？！

2FA 也称为多因素身份认证或双因素身份认证，为身份认证机制提供了额外的安全层。
维基定义:

> **多因素认证** ( **MFA** )是一种[认证](https://en.wikipedia.org/wiki/Authentication)方法，其中[计算机
> 用户](https://en.wikipedia.org/wiki/Computer_user)只有在成功向
> [认证](https://en.wikipedia.org/wiki/Authentication)机制出示两个或更多个
> 证据(或因素)后
> 才被授予访问权:知识(用户知道且只有
> 用户知道的东西)、占有(用户拥有且只有用户
> 拥有的东西)、继承(只有用户知道的东西)[【1】](https://en.wikipedia.org/wiki/Multi-factor_authentication#cite_note-1)[【2】](https://en.wikipedia.org/wiki/Multi-factor_authentication#cite_note-2)
> 
> **双因素认证**(也称为 **2FA** )是多因素认证的一种类型或子集。这是一种通过使用*两个*不同
> 因素的组合来确认
> 用户声称的身份的方法:1)他们知道的东西，2)他们拥有的东西，或者 3)
> 他们是什么东西。
> 
> 双因素认证的一个很好的例子是从
> [ATM](https://en.wikipedia.org/wiki/Automated_teller_machine) 提取
> 的钱；只有[银行卡
> 卡](https://en.wikipedia.org/wiki/Bank_card)(用户拥有的东西
> )和
> [PIN](https://en.wikipedia.org/wiki/Personal_identification_number) (用户知道的东西)的正确组合允许
> 交易被执行。
> 
> 另外两个例子是用只有
> 用户
> 拥有的
> [认证器](https://en.wikipedia.org/wiki/Authenticator)(例如安全令牌或智能手机)生成或接收的一次性密码 (OTP)或代码来补充用户控制的密码。[【3】](https://en.wikipedia.org/wiki/Multi-factor_authentication#cite_note-IOVATION-3)
> 
> **两步验证**或**两步认证**是一种利用用户知道的东西
> (密码)和除了他们
> 拥有的东西或他们是什么以外的第二个因素*来确认用户声称的身份的方法。第二步的一个例子是用户
> 重复通过
> [带外](https://en.wikipedia.org/wiki/Out-of-band)
> 机制发送给他们的东西。或者，第二步可能是由用户和[认证
> 系统](https://en.wikipedia.org/wiki/Authentication_and_Key_Agreement)共用的[应用](https://en.wikipedia.org/wiki/Mobile_app)
> 生成的
> 六位数。[【4】](https://en.wikipedia.org/wiki/Multi-factor_authentication#cite_note-4)*

网飞目前不提供任何形式的上述担保。为什么？许多人声称工程上的努力不值得，或者他们的信息不是需要保护的私人信息。我会对这些观点进行论证，并声明您的:

*   邮寄地址
*   帐单地址
*   您的信用卡或 PayPal(或付款方式)的后四位

会被认为是私人信息。网飞凭证的未授权登录共享的问题很猖獗；2FA 将有助于防止这种情况。

问题是- **你对网飞为什么还没有对其用户实施增强的安全措施有什么看法？为什么没有 2FA？**