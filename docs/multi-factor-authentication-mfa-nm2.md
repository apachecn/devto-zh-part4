# 多因素身份认证(MFA)

> 原文：<https://dev.to/azure/multi-factor-authentication-mfa-nm2>

### 多因素认证(MFA)

###### 本文面向安全初学者或其他 IT 人士，非安全专家。:-D

最近，你可能已经注意到我指出了几家加拿大银行不允许用户在他们的网上银行账户中添加多因素认证(MFA)。这篇博文将详细说明我这么说的意思，为什么它很重要，以及我为什么要推动它。

#### 更新:你可以通过标签#MFAally 在线关注这个主题的社区活动。

[![Me, hassling a Canadian Bank about their lack of MFA](img/a0c043abad8c65c77d413b93723ee8bb.png "Me, hassling a Canadian Bank about their lack of MFA")T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--ldhpKahE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8boelopw0meiyw9ti5oc.png)

双因素或多因素身份验证(2FA 或 MFA)意味着使用多个因素来证明您是真实可信的您。认证的一个“因素”是向计算机证明你是谁的一种方法。目前只有 3 种类型:你**有的东西**，你**有的东西**，你**知道的东西**。

**“您拥有的东西”**可以是您的笔记本电脑、手机、生成代码的令牌、您的徽章等等。你可能是你的指纹，虹膜扫描，你的步态(你走路的方式)，或者你的 DNA。对你来说身体上独一无二的东西。

[!["Something you have"](img/85d1f77f3f16ae1c066932c934c01851.png "Something you have")](https://res.cloudinary.com/practicaldev/image/fetch/s--PGFuzVt_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2645qwwd6pum65a4z4zu.jpg)

**“你是什么”**可以是你的指纹、虹膜扫描、你走路的步态、你的 DNA 等等。

[!["Something you are"](img/45c4216ce99fc9d7b986f53639e377d3.png "Something you are")](https://res.cloudinary.com/practicaldev/image/fetch/s--UJkMh0qk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rjc9j8dr8lbf153hbykt.jpg)

**“你知道的事情”**可以是密码、通行短语、模式或几条信息的组合(通常称为“安全问题”)，如你母亲的婚前姓氏、你的出生数据和你的社会保险号。这个想法是，这只是你知道的事情。

[![**"Something you know"**](img/1591ccf8c7f675a98e856093a7697c14.png "Something you know")](https://res.cloudinary.com/practicaldev/image/fetch/s--iifNMXV---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jyrlb3xqgv0d2w9mholz.jpg)

当我们仅使用用户名和密码登录帐户时，我们只使用了一个身份验证“因素”,这比使用两个或更多因素更不安全。很多时候，帐户被闯入或数据被盗，往往是由于有人只使用一个因素。
当密码被破解时，拥有第二个身份验证因素的用户仍然受到保护。当有人试图暴力破解启用了 MFA 的系统或帐户时，即使他们最终获得了密码，他们也不会有第二个因素来进入。使用第二个因素会使你的在线账户更难被侵入。

#### MFA 的例子

*多重因素*:输入您的用户名和密码，然后必须使用第二个设备或物理令牌来接收代码以进行身份验证。用户名和密码是一个因素(你知道的东西)，使用第二个设备是第二个因素(你有的东西)。

*非多因素*:一个用户名和一个密码。这是同一个因素的两个例子；他们都是你知道的东西。多因素身份认证意味着您有多个不同类型的身份认证因素，而不是一个或多个相同类型的身份认证因素。

*非多因素*:使用用户名和密码，然后回答安全问题。这是两个*相同的*事实，一些你知道的事情。

[![Microsoft Authenticator app](img/5bfc64e55fd1ab174bbd17463912c315.png "Microsoft Authenticator app")](https://res.cloudinary.com/practicaldev/image/fetch/s--rl4Lw-6u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8x4mfix9tl4rn4vjkfdj.jpg) 
当云壳把我从舞台上注销的时候(好尴尬！)去年冬天在香港的 [#MSIgniteTheTour](https://www.microsoft.com/en-ca/ignite-the-tour?WT.mc_id=devto-blog-tajanca) 上，我使用了我的用户名和密码(我知道的两件事，意思是同一因素的两件事)，加上[微软认证应用](https://www.microsoft.com/en-us/account/authenticator?WT.mc_id=devto-blog-tajanca)(我有的东西)，在我的手机上(我有的其他东西)，它要求我的指纹(我就是这样的东西)。这意味着我使用所有三个身份验证因素重新登录。尽管我知道它无意中制作了一个我正在使用的微软产品的演示，但在演示过程中被注销还是很尴尬的...
Demo-failure 暂且不说，先来说说 MFA 是什么，不是什么，为什么这么重要。

#### 我们行业中的许多人对于使用您的手机接收带有 pin 的 SMS(文本消息)是否是 MFA 的“好”实现存在分歧，因为 SMS 协议及其一些实现中存在已知的安全缺陷。我(可能不受欢迎)的观点是，我宁愿有一个相当好的第二身份验证因素，而不是只有一个因素，如果这是说服普通用户采用 2FA 的折衷(便利性与完美的安全性)，我赞成使用 SMS 作为第二个因素。

Azure 安全中心给任何人的第一条安全建议是对你的所有订阅启用多因素身份验证；保护你的(云)王国的钥匙是最重要的。事实上，启用多因素身份验证(简称为 MFA 或 2FA)是行业最佳实践，安全专业人员经常向技术人员和非技术人员这样描述他们所有的重要客户。然而奇怪的是，[在谷歌](https://hackernoon.com/why-do-most-people-ignore-two-factor-authentication-1bbc49671b8e)和其他流行平台上只有不到 10%的账户启用了 2FA。为什么？

我怀疑原因是 2 折；1)这并不总是方便的，2)公众根本不了解这种风险。虽然我们大多数人无法改变第一，但我们每个人都可以努力改变第二。

亲爱的读者，我想呼吁你，试着向你生活中、工作中或家里的人解释 MFA，并请他们在他们的重要账户上启用它。我还想请你在家里和工作中为自己启用 2FA，如果你还没有的话。它可能会让你或你爱的人免于严重的心痛。

感谢您的阅读。

**关于这一点和更多，请查看我的书，[爱丽丝和鲍勃学习应用安全](https://aliceandboblearn.com/)和我的在线培训学院，[我们黑紫色](https://academy.wehackpurple.com)！**