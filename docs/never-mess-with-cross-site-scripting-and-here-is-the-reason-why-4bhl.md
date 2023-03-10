# 不要乱用跨站脚本，原因如下

> 原文：<https://dev.to/nileshsanyal/never-mess-with-cross-site-scripting-and-here-is-the-reason-why-4bhl>

[![Cross Site Scripting](img/0503caebed7337cea168b19736207738.png "Cross Site Scripting")](https://res.cloudinary.com/practicaldev/image/fetch/s--l_QK-MGO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-nfY7h_khtmg/XRhdzb8pyHI/AAAAAAAABFg/3Y0iE-IHj10bo2eIS2r5-4A4Dar6KeQOQCLcBGAs/s640/xss.jpg)

*阅读原文[此处](https://www.devhelperworld.in/2019/07/cross-site-scripting.html)T3】*

在**跨站点脚本**攻击中，也称为 xss，客户端代码以 html 属性的形式被注入到网页的输出中，并在用户的浏览器中执行。成功利用的影响各不相同。

## 我为什么关心跨站脚本？

**跨站点脚本**攻击者可能会利用漏洞来实现一系列潜在的恶意目标，包括:

1.  窃取您的会话标识符，以便他们冒充您并访问在线应用程序。

2.  将您重定向到收集敏感数据的网络钓鱼页面。

3.  在你的电脑上安装恶意软件(通常你的浏览器和操作系统需要零日漏洞)。

4.  代表您执行任务(即使用攻击者的凭据创建一个全新的管理员帐户)。

### 不同类型的跨站脚本解释

有 3 种类型的 xss，如下所示...

#### 体现了跨站脚本:

[![Reflected Cross Site Scripting](img/f283bf534fbe82bd2573de041852f1d0.png "Reflected Cross Site Scripting")](https://res.cloudinary.com/practicaldev/image/fetch/s--JI3s4EdV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-vokBKtEF0o4/XRhg2xaadRI/AAAAAAAABF4/BMB2WlaN9AMsA2IoT0xpx2p1yJxAzKWoQCLcBGAs/s640/reflected_xss.jpg)

在这种类型的攻击中，易受攻击的页面将被用来执行脉冲代码。这种类型的 xss 不会跨多个请求保存攻击代码。

由于攻击者必须将用户发送到一个特制的链接，代码才能运行，反射 XSS 有时需要一些社会工程来成功执行攻击。

#### 真实世界场景的跨站点脚本:

让我们假设，山姆是一个攻击者，他在中发现了一个反射的 xss 漏洞。Jhon 是该网站的用户，Sam 向 Jhon 发送了一封假电子邮件，声称他是 Iphone 的幸运中奖者，并要求他点击电子邮件中提供的链接。

约翰在登录[www.site.com](http://www.site.com)时点击链接。该脚本在约翰的浏览器中执行，它窃取约翰的与 www.site.com 的 T2 相关的会话 cookies 并发送给萨姆。

通过使用会话 cookie，Sam 可以轻松地访问 Jhon 的个人信息和其他数据，如危及 Jhon 帐户的信用卡或借记卡数据。

#### 存储的 xss:

[![Stored XSS](img/2b0bf20e2ababe3ed3d6e8a0f8bf3553.png "Stored XSS")](https://res.cloudinary.com/practicaldev/image/fetch/s--dQfxj8Ot--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-EK894cZFViA/XRhhHIs1uBI/AAAAAAAABGA/Euxp7pIz9GYdoyD5iFgSRAfkELmzireegCLcBGAs/s640/stored_xss.jpg)

在这种类型的攻击中，攻击者提供的恶意数据将存储在服务器中。它比反射的 **xss** 更危险，原因如下。

首先，存储的 xss 攻击将是自动的。访问数千个网站的脚本可以利用每个网站上的漏洞，并丢弃存储的 xss 有效负载。

第二，受害者不需要采取任何行动，除了访问受影响的网站。任何访问网站上受影响页面的人都将成为存储恶意代码的受害者。发生这种情况是因为脚本会加载到他们的浏览器中，受害者不需要采取额外的行动。

#### 存储 xss 的真实场景:

假设，是一个拥有成千上万用户的论坛，用户可以就不同的技术主题发表帖子，还可以对每个帖子发表评论。Sam 决定通过利用评论功能来获取用户凭证。他写了几行注释，然后制作了一个恶意链接，充当 read more 链接。

当论坛用户阅读了他的几行评论，并好奇阅读其余部分时，他们会点击**阅读更多**链接。该链接将激活一个 javascript 文件，窃取用户的会话 cookies。

#### 基于 DOM 的 xss:

[![DOM Based XSS](img/7e911e99e8c3eed3c19cd8d5690b38cd.png "DOM Based XSS")](https://res.cloudinary.com/practicaldev/image/fetch/s--nR7wAvIT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-eHbsYo8SbhI/XRhhQGwvG6I/AAAAAAAABGE/c5iuwJKouigXuXo_s0KlKOGO0lZSyN-cACLcBGAs/s640/dom_xss.jpg)

这种类型的攻击通常围绕 DOM(文档对象模型)展开。在其他类型的攻击中，受感染的代码来自服务器端到客户端。但是，在这种类型的攻击中，javascript 代码被用来操纵 DOM。

#### 基于 DOM 的 xss 如何可能？

这是可能的，部分原因是基于 javaScript 的高级应用程序从不同的地方获取信息并处理它们。一般来说，这个过程最终会创建 DOM 对象或在浏览器中直接执行 JavaScript。

DOM **xss** 由两个事实组成:源和接收器。源是包含用户输入的东西。接收器被认为是浏览器执行用户输入的地方。

## 结论:

在本文中，我简要讨论了为什么您不应该忽视跨站点脚本。我希望，你会发现这篇文章对你有帮助。