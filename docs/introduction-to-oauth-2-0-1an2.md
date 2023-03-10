# 用 OAuth 2.0 走一走

> 原文：<https://dev.to/tech_sam/introduction-to-oauth-2-0-1an2>

[![OAuth2.0](img/60078a9720e62880e3cf55201808fca0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YpW-k5_e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8kj3ivyuetyy49ikelkc.png)

最近我得到了一个开发 OAuth 2.0 的机会，我注意到互联网上有如此多的复杂性和混乱。

背后的原因是，这是一个很难理解的协议，大量的术语和术语，网上有很多关于它的混乱。

当我开始学习 OAuth 2.0 和 OpenId connect 时，我没有找到一个真实的来源，有太多的博客，StackOverflow 上的每个人似乎都在以他们自己的方式解释如何实现它。

如果你曾经感到困惑，不知所措，那么你不是唯一一个，几乎每个人都有。在这个博客中，我会试着让事情不那么令人生畏，在评论中，你可以让我知道它是否有意义。

为什么我们有一个具体的规格说明，但是对于如何使用它却有这么多不同的解释？像 HTTP 这样的协议不会发生这种情况，有一种非常正确和具体的方法可以使用，但对于 OAuth，实现中存在一些模糊性，这使得它更加困难，并对如何使用它产生了困惑。

因此，在解释 OAuth 和 OpenId 之前，我只想为简单的登录和表单身份验证搭建一个平台，这是 web 上最基本的身份验证类型，用户输入用户名/电子邮件和密码，请求发送到服务器，服务器与数据库通信并验证用户名和密码(希望是散列的)😉。

如果验证成功，服务器将向 web 浏览器发送一个 cookie 来跟踪用户。

[![Basic Form Authentication](img/cbbec3d654a4047894b81886ed816b91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rI4UE_aF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3m55hquzkm1qfmqp91vq.png)

现在，行业正在远离这种自行开发的解决方案，这种简单的表单认证与 OAuth 无关，但我只想让您知道，这是我们要比较的。

这种方法有几个缺点，比如

*   安全性
*   维护

因为您负责维护身份验证系统，所以只有您的服务器端代码会与数据库对话并验证密码等等。您应该始终了解新的安全标准和最佳实践，如哈希、加密和安全存储用户信息等。

让我们回到 2006-07 年，那时的应用程序和网站也有几个用例，如登录、身份验证、授权等
,因此我们可以将这些用例结合起来，称之为身份用例。

身份使用案例(2010 年之前)

*   简单登录(表单和 cookies)
*   跨站点单点登录( [SAML](https://www.varonis.com/blog/what-is-saml/) )
*   移动登录(如果应用关闭，用户也应保持登录状态`long living sessions`)
*   委托授权(？？？？)

这里，术语委托授权是 OAuth 协议的 Genesys。尽管听起来很无聊，但这是我要关注的事情。

您可能知道委托授权，以及您每天都要交互的东西，但实际上并不知道它是什么。这是一种常见的模式，但不是 10 年前。

委托授权是一个过程，在这个过程中，我们让特定的网站访问我们的数据，而不实际给出凭证。

[![Delegated authorization Sample](img/80c08f7d000888521214f8652b80ba35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hMsk3peN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/29ueymi41brm8p169gj4.png)

因此，当时没有好的方法来解决这个问题，一家名为 Yelp 的公司试图解决这个问题，但用了一种非常糟糕的方法
在注册结束时，Yelp 问用户，嘿，你想与你的 Gmail 或雅虎联系人分享 Yelp 来注册或促销等吗
为此，他们想要用户的实际 Gmail 电子邮件地址和密码，这意味着就像 Yelp 登录用户的 Gmail 帐户，抓取用户的联系人向他们发送电子邮件，然后注销并扔掉密码，并承诺不做任何坏事😱

[![Yelp Delegated authorization Solution](img/caa78d5c1de8a0421a8f2728fc9eff6e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QjiJTdhM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/89t15zdxblvhugd9nknj.png)

对许多人来说，他们的 Gmail 或 Yahoo 帐户是他们的主要电子邮件帐户，是他们拥有的一切的关键，如银行资料、财务、密码重置等。因此，将他们的密码随机提供给任何应用程序或网站都是一个非常糟糕的主意。

在这里，我不是针对 Yelp 来设计这种类型的解决方案，这只是一个很好的例子来说明问题是什么，然后他们如何没有适当的方法来解决问题。

今天我们有一个更好的方法来解决这个问题，使用 OAuth 协议，就像上面的图片，Gitlab 想要访问 Github 用户信息。

现在我们将看到 Yelp 应该如何使用 OAuth 解决这个问题。

[![Delegated authorization with OAuth 2.0](img/14855ff3d4b71a128bf3686b4de130f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CYubnl-i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wmmd6mewjg93wobcdlid.png)

如今，我们在每个应用程序上都有一个链接/按钮，类似于`connect with google`，当用户点击这个按钮时会发生什么，用户将进入 OAuth 流程，最终结果是，应用程序可以访问用户的授权信息。

用户可以确保希望访问联系人的应用程序只能访问联系人，而不能删除联系人、发送电子邮件或查看 google photos 和 drive 信息上的照片等。

当用户点击`connect with google`按钮时，浏览器将重定向到类似`account.google.com`的谷歌登录页面，这比之前的解决方案好一点，因为用户将密码提供给谷歌，而不是 Yelp 或其他人。

[![Delegated authorization with OAuth 2.0](img/ac86ef088b05a2d9a7a22354891dfa72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r4_RFRyw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d75wfnez2mkcjrgzbesw.png)

假设现在用户成功登录，然后会出现一个提示，因为这个应用程序 Yelp 想要访问您的信息列表，其中包括您的公共配置文件或联系方式等，你确定要允许访问 Yelp 这些信息。

[![Consent](img/ad6b07d5359f1dce5ac8ad89dce8ad44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d1HaeMPu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3460x773smxeuwvtlda8.png)

现在，用户明确同意他们授予访问权限的任何内容，这很重要，这样他就不会被欺骗，比如同意他不知道的内容。

因此，如果用户单击否，那么我们就完成了，这里不会发生任何有趣的事情，流程将再次重新开始，但是假设如果用户单击是，那么浏览器再次重定向回应用程序，回到它开始的地方，应用程序中的一个特殊位置，称为回调或重定向回调或重定向 URI，我们将在博客中稍后讨论。

然后，通过一点小魔法，在我们的例子中是 Yelp 的应用程序被允许与一些其他 API 对话，比如 google contacts API，Yelp 通常不能访问这个 API，但是现在它有了一些东西(`access_token`)，当用户点击“是”按钮时，这个应用程序得到了这些东西。

[![OAuth 2.0 magic](img/47bc36ff76d6c4cb18ee914f47112cdd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eTxzQcdj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uyp4o4szb6q0m9qv4flv.png)

现在，下面是进入这个魔术的东西。

### OAuth 2.0 术语

*   资源所有者
*   客户
*   授权服务器
*   资源服务器
*   授权许可
*   重定向 URI
*   访问令牌

资源所有者是一种谈论你和我的奇特方式，作为一个点击“是”按钮并坐在电脑前的用户，拥有 Yelp 想要访问的应用程序的数据，在这种情况下，我是拥有 Gmail 帐户和联系人的资源所有者，Yelp 想要访问我的 Gmail 联系人。

客户端只是指应用程序的一种方式，在这种情况下 Yelp 是客户端，基本上是需要资源所有者数据的应用程序。

授权服务器是一个系统，资源所有者可以使用它来说是，我授权了这个许可，并且我授权了这种情况发生。在这种情况下，授权服务器是`accounts.google.com`。
如果你在谷歌上搜索[授权服务器](https://support.okta.com/help/s/article/What-is-an-Authorization-Server)，你会看到😧！！这就是我写这篇博客的原因，让事情变得更清楚一点。

资源服务器不同于授权服务器，资源服务器是 API 服务器，它保存了客户端想要访问的数据，在这种情况下，Google contact API 是保存我的联系人的系统。

有时授权服务器和资源服务器融合在同一个系统中，但很多时候它们是分开的。

OAuth 流程的整个要点是转到授权服务器，返回到客户端只是为了获得所谓的授权许可，授权许可是证明我已经点击了“是”或我同意这一级别的权限或我允许你访问我的 Gmail 联系人的东西。

当授权服务器重定向回客户端应用程序时，它需要知道重定向回哪里，这称为回调，有时也称为重定向 URI，这就是如果用户单击“是”，他们接下来需要去哪里，那么我应该在这个流程的末尾结束。

我在上面提到过，授权是整个流程的关键，但是在更高的层次上，客户端真正需要的是一种叫做访问令牌的东西。
访问令牌将是客户端用来访问数据用户在资源服务器上授予访问权或权限的密钥。

如果你一直读到现在，如果你准备好表现出一点耐心，我想你会感到困惑和不知所措，你所有的疑惑和困惑都会很快消除。

让我们看看上面的图表，我之前已经用所有这些术语描述过了。

现在，我们将再次开始流程，我是资源所有者，他正坐在客户端网站 Yelp 上，我单击了`connect with google`按钮，然后我将重定向到这个授权服务器，在本例中是 accounts.google.com，但它可能是脸书授权服务器或 Octa 托管的授权服务器，也可能是其他人。

[![Flow-1 with jargon](img/7a0722a6beb10490f5863411f074a5db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--auGBlrxW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g1t6bkmip7mhy1riy451.png)

在这个流程的开始，当客户端通过授权服务器重定向时，它已经传递了授权服务器需要的某种配置信息，所以它会说，嘿，当你完成时，假设一切都成功了，这里是 URI，我希望你在最后重定向回来。因此，我们必须在一开始就通过重定向 URI，我们还必须提供一些其他信息，如我们想要什么类型的授权。

实际上有几种不同类型的授权，在这种情况下，我们将使用一种最简单的授权类型，称为代码授权或授权代码授权，因为我们请求的是一个代码，它称为授权代码流。

现在授权服务器提示登录，同意该许可所有好的东西然后重定向回开始指定的地方，重定向 URI，用所谓的授权码重定向，因为这是我们开始时要求的。

[![Flow-2](img/37f04349f45bbfb6a4e24115442ea0ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HZqlJbSY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4zp6k9uheu0lup0bmejs.png)

现在，客户端无法使用该授权码做很多事情，事实上，客户端只能使用授权码做一件事，那就是再次返回授权服务器，与`access token`交换该授权码。

[![Flow-3](img/f24e329520a3ff8ce7b13fb9a9649cb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6edKC1dd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qqtovnzm1y3316gdhts7.png)

获得访问令牌后，现在客户端 Yelp 可以做他们实际上希望我们首先做的事情，即访问资源服务器`contacts.google.com`并请求用户联系。

通常资源服务器不允许访问用户的联系人，但是在这种情况下，客户端在请求中附加了访问令牌，这证明用户说资源服务器可以访问联系人信息。

[![OAuth 2.0 flow](img/3a005355391cceb2ce4154ff4cf0581b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IKnEHzPg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y8hyrqu8111368h6aqyg.png)

现在，如果客户端尝试做其他事情，比如不检索我的联系人，而是删除我的所有联系人，在这种情况下，资源服务器会说“确定”,您有一个访问令牌，但这并不意味着您可以做任何事情，用户只给了您对联系人的只读访问权限。

因此，访问令牌授予客户端访问权限来做特定的事情，但是客户端如何指定它想要做的事情呢？？

让我们再添加一些术语。

*   范围
*   同意

授权服务器具有基于客户端需求的作用域列表，例如，对联系人的只读访问。

太多了，好吧，我知道了，让我们在下一篇博客中继续讨论范围和同意。

*关注我的[媒体](https://medium.com/@tech_sam)和[发展到](https://dev.to/tech_sam)，了解更多科技知识。*