# 网络安全未知:同源策略

> 原文：<https://dev.to/merictaze/web-security-unknowns-same-origin-policy-3l6>

像 CSRF 或 XSS 这样的袭击对我们许多人来说仍然不清楚。这并不是因为它们非常难以理解，而是需要对 SOP、CORS、CSP、HTTPS 等概念有一些基本的了解。在这一系列的文章中，我将试着一个一个地澄清它们，下次你读到关于攻击的内容时，你就会明白一切了。

[![](img/8ce63d881e2ccaa87efb9949ba84898d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XnkexF_r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AhkW7ucp197z4ZdWB) 

<figcaption>照片由[兰迪神父](https://unsplash.com/@randyfath?utm_source=medium&utm_medium=referral)于 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

### 什么是同源策略(SOP)

这是大多数浏览器实施的一种安全措施，用来保护用户免受坏人的攻击。这是为了保证你的安全，它不是什么新东西。它可以追溯到旧的网景时代。它做的很简单，阻止从其他域读取资源，只允许同源。

这里的资源可以是您在页面中嵌入的 iframe 的 DOM、http 请求中的 JSON 响应或 cookies。

为什么会存在？它可以保护您免受恶意脚本的攻击，这些脚本可能会在您访问的网站上运行。尽管你访问的是 evil.com，运行在这个网站上的脚本可以向 my-bank.com/get-account-details 的 T2 发出请求，或者嵌入一个带有源 my-bank.com/get-account-details 的 iframe，然后试图读取响应/DOM 来访问你的数据。如果你认为*“没关系，因为他们不知道我的身份”*，请三思。浏览器将自动为*my-bank.com*使用现有的 cookies。这意味着如果你在 my-bank.com 上有一个活跃的会话，恶意脚本不需要你的凭证。

还有一些对 SOP 的常见误解。它不会阻止向后端发送请求。它只阻止读取响应。*“太好了，让我在我的恶意脚本中触发一个对 mybank.com/transfer-money 的呼叫。我只希望请求到达后端，我不在乎响应，反正我会在我的银行账户中看到它"*你可能会说。没那么快。当然，从 SOP 的角度来看，这完全没问题。但是，还有其他安全措施来防止这种情况。这是你应该真正了解 SOP 的最重要的方面。我们再重复一遍:你可以自由地向任何域发送任何请求，SOP 不关心请求，它只阻止读取其他域的响应。

由于上述同样的原因，SOP 不阻止请求，它对 CSRF 攻击没有帮助。这种攻击依赖于发送带有该域已有 cookies 的请求。它不关心回应。

如果你说*“是的，我这里没有消息”*，太好了！可以不看了。否则，继续前进。

### 什么叫做同源

**协议** (http，https)**+主机**(example.com)**+端口** (8080)

给定上面的定义，[http://example.com/abc](http://example.com/abc)和[http://example.com/xyz](http://example.com/xyz)将是同源的，但不是

*   [http://example.com:8000/abc](http://example.com:8000/abc)因港口不同
*   [https://example.com/abc](https://example.com/abc)由于协议不同
*   [http://test.example.com/abc](http://example.com/abc)因宿主不同

### 缓兵之计

请注意，以下示例假设后端没有额外的安全检查，并且只关注 SOP。

#### iframe

当你输入*gmail.com*时会发生什么？假设你有一个帐户，并且之前已经登录过，它会显示你所有的电子邮件。假设我拥有 evil.com 的 T2，并使用一个 iframe 在其中嵌入了 gmail.com 的 T4。一旦你点击*evil.com*，你能想象如果我作为*evil.com*的所有者用 JavaScript 读取 iframe 中的内容会出什么问题吗？是的，你猜对了，我可以阅读你所有的电子邮件，甚至保存在一些数据库中。但是，不用担心。因为你在 evil.com 上，在这个域上运行的脚本将不能从另一个域的 iframe 中读取 DOM 内容。*gmail.com*。

SOP 保存了这一天，并阻止读取属于另一个域的 iframe 的 DOM。

#### http 请求

我听到你们中的一些人说*“我从未使用过 iframe，你有其他的例子吗？”*。好，让我们举一个没有 iframes 的例子。我拥有 evil.com 的 T2，你参观它，因为你不知道我做坏事。我在 evil.com 的*中添加的 JS 代码*向 gmail.com/get-all-emails 的*发送一个 GET 请求。你认为我能发送它吗？*

答案是肯定的(什么？！).再次指出，SOP 不为请求做任何事情，它只阻塞响应。我的请求将被发送到 gmail，浏览器将获得 200 成功。然而，当我的脚本试图查看响应时，它无法看到，当你在 Chrome dev tools 中检查网络/响应选项卡时，你也无法看到。浏览器会像 SOP 承诺的那样屏蔽它。(这可以通过使用 CORS 来放松)

SOP 再次拯救了我们，阻止读取来自另一个域的 http 请求的响应。

#### 饼干

Web 主要依靠 cookies 进行身份验证。Web 服务器为它的域设置一些 cookies，这样当它收到您的请求时就可以识别您的身份。为了简单起见，假设当你登录到*gmail.com*时，它为域名 gmail.com 设置了一个 cookie 用户名= "[test@gmail.com](mailto:test@gmail.com)。每当有一个请求发送到 gmail.com，你的浏览器就会添加所有属于该域的有效 cookies。也就是说，当你给 gmail.com/get-all-emails 的*打电话的时候，它也会在你没有意识到的情况下发送用户名= "[test@gmail.com](mailto:test@gmail.com)"Gmail 的网络服务器会从 cookie 中看到你是谁，并返回你所有的邮件。*

在现实世界中，事情没那么简单。Gmail 实际上设置了一些东西来识别你，但它更复杂，如果任何人改变了字符串的任何部分，他们都会知道并拒绝响应你的请求。

我想现在已经很清楚了，谁有那个字符串，谁就能得到你所有的邮件。所以，对于浏览器来说，不将 cookies 从一个域暴露给另一个域是非常重要的。同样，作为*evil.com*的所有者，我可以代表你向*gmail.com*发送请求，浏览器将包含属于*gmail.com*域的所有 cookies。但是，在 evil.com 上运行的脚本将无法读取 cookies 和服务器的响应。耶！

关于这一点只需注意一点:不同的协议和端口不会使 cookies 成为跨域的资源。例如，[http://example.com](http://example.com)，【https://example.com】T2，[https://example.com:7000](https://example.com:7000)被认为是同源。

#### 表格

*“如果我使用表单呢”*我听到你问。假设我做了一些 JS 的小把戏，用我在 evil.com 的*上运行的恶意代码提交了一个表单，它用我在代码中提供的有效载荷向 gmail.com/send-email 的*发送了一个请求。这里也一样，浏览器会发送它。但是提交之后，浏览器会把你从*evil.com*带到动作 url(即。*gmail.com/send-email*)，所以*evil.com 无法*读取来自其他域的响应，因为用户被重定向到 *gmail* 。请注意，这不是 SOP 的特性，只是浏览器中默认的表单行为。**

### 所有来自另一个域的东西都被屏蔽了吗？

没有。你仍然可以

*   嵌入 iframe
*   使用图像、音频、视频文件
*   导入脚本和 css 文件
*   提交表单

然而，你

*   无法读取您嵌入的 iframe 的 DOM
*   无法读取图像、音频和视频文件的文件/字节。
*   无法读取您导入的 JS、CSS 文件的内容
*   无法读取提交表单的响应

除此之外，还有一些放松 SOP 的技巧。您可以使用跨源资源共享(CORS)、带填充的 JSON(JSONP)以及其他一些技术。请注意，这不是浏览器可以单独解决的 SOP。前端和后端都需要正确配置，以实现跨域资源的访问。

### 总结起来

浏览器执行 SOP 来保护你的资源安全，因为当你访问*evil.com*时，你永远不知道幕后发生了什么。SOP 不阻止采取行动(发送请求等)，但确保坏人无法看到该行动的结果(读取响应/cookie/DOM)。

它不会阻止你使用来自其他领域的图像，视频，脚本，css 文件。CDNs 万岁！还有一些放松 SOP 的方法，比如使用 CORS、JSONP。

希望这篇文章能让你对同源政策有所了解。继续阅读我其他帖子中的其他未知内容，以了解全貌。欢迎在下面留下评论。