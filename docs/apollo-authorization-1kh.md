# 阿波罗授权

> 原文：<https://dev.to/vazcoeu/apollo-authorization-1kh>

几周前，我不得不在一个 Apollo 应用程序中通过 Google API 创建一个授权过程。也许，如果我可以用一种简单的方法，使用 [express.js](https://expressjs.com/) 服务器和 [passport.js](http://www.passportjs.org/) ，事情就不会如此严重。然而，当我们使用 GraphQL/React 应用程序时，我需要专门使用 Apollo server。

我第一次必须成功，所以我到处寻求帮助。因为我在一家很棒的公司工作，所以我不用找很远的地方。我的一位资深同事向我详细解释了如何做到这一点。这反过来促使我写这篇文章。这是一条漫长的道路，伴随着相当多的副产品。我希望它能帮助你们让事情变得简单。

[阿波罗和谷歌授权如何运作。](https://developers.google.com/identity/protocols/OAuth2)

[![](img/fe153e5e648bff14e9b28ed5a8fab145.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1eWYck7M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hrr8spp9w4iz9zjw15u1.jpg)

这个方法看起来很简单，其实不然。这不是一个复杂的 JavaScript 代码，但纯粹的过程会让你头疼。

让我们从解释如何组织谷歌授权开始。

(1)首先，你必须在 [Google 控制台](https://console.cloud.google.com)中注册你的 app，登录并选择 API，然后选择凭证。

[![](img/6acd2b7ea391e3588760c13c949736e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NLLQ8nDb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c2jba6rvxjwtjrf97jd8.jpg)

下一步是选择“创建 OAuth 客户端 ID”。选择应用类型:“Web 应用”，命名您的应用并设置 URI:例如:`localhost:3000`。

之后，当你点击你的应用程序，你会得到客户 ID 和客户密码。这两个键对于我们接下来的步骤是必要的，因为我们将使用它来与 Google API 通信。在书签“OAuth2 同意屏幕”中，您可以设置一个授权域，告诉我们用户可以从哪个域登录。

(2)最后，是时候让你的手握一个舵，开始真正的发展了。我认为有许多不同的方法可以做到这一点。其中一个，也是相当著名的一个，你可以在这里找到。

我或多或少地遵循了这个例子，同时也试图将许多方法结合在一起。

现在让我们制作一个简单的登录按钮:

[![](img/f5c1f2c36028b475a97acc04bd2ff3e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HWymD2K8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2wvkkzi06x5kqu72zg0r.jpg)

我希望你知道如何使用 CSS。然后，我们把一些逻辑放在后面。

我使用了 [react-create-app](https://github.com/facebook/create-react-app) ，因此我浪费了一些时间去寻找应该把外部代码放在哪里。但事实证明，解决方案很简单。将其放入`<head>`部分的`public/index.html`: