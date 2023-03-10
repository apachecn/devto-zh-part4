# 2019 年 JSON Web 令牌放在哪里

> 原文：<https://dev.to/kabisasoftware/where-to-put-json-web-tokens-in-2019-3pio>

几年前，我在埃因霍温的一次 Java 爱好者聚会上做了一个关于 JSON Web token(jwt)的演讲。由我最近参加的一个关于 jwt 的讲座引发，我决定重温我的演示文稿和我当时制作的演示应用程序，看看它们是否仍然有效。
事实证明，2019 年的生活比 2016 年要艰难一点，至少在安全和 jwt 方面是这样。
在我们深入探讨这个意见的细节之前，我们应该先讨论一下基本情况。

## JSON Web 令牌

从本质上讲，JSON Web 令牌是服务器应用程序提供给客户机应用程序的东西，客户机在请求时用它向服务器验证自己。JSON Web 令牌看起来像这样:

**eyjhbgcioijuzhmij 9。eyjlehaioje 0 nzyotaxndksinn 1 yi i6 ije ifq。mvjewu 3k XM 0 wsuku-qEVTBmuelM-2Te-VJHEFclVt _ ur 89 ya 0 hnawkrgftqbad-28 lyclx 2 jxcgogra 3 xrg 9 jg**

如果仔细观察，您会发现它由三个 base64 编码的字符串组成，由句点连接。如果你对上面的进行解码，你会得到如下结果:

```
{
  "alg": "HS512"
} 
```

```
{
  "exp": 1476290149,
  "sub":"1"
} 
```

```
HMACSHA512(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret
) 
```

第一部分是报头，第二部分是有效载荷，第三部分是签名。任何拿到这个令牌的人都可以解码这些字符串。
(如果你想亲眼看看，在你的浏览器控制台中执行`atob("eyJhbGciOiJIUzUxMiJ9")`)。)
这意味着任何拿到令牌的人都可以使用编码信息。
因为只有服务器知道用于从头部和主体计算签名的秘密，然而，只有服务器可以通过重新计算其预期签名并将其与实际签名进行比较来检查令牌的有效性。
一旦服务器确定一个给定的 JWT 是有效的，它就知道它自己发布了令牌，并且主体中的数据是可信的。

报头指定了用于计算签名的算法。在这种情况下，这就是 HMAC-SHA512 算法。

有效负载可以包含任意数量的声明。
在本例中，使用了标准权利要求`exp`和`sub`。
声明`exp`(到期时间的简称)指定令牌何时到期。
声明`sub`(“subject”的缩写)指定了令牌的主题，通常类似于您的应用程序的用户，由一个标识符表示。
还有许多其他标准声明，您可以自由添加自己的声明。

## 一趟记忆的小路

当我第一次读到 JWTs 的时候，我仍然习惯于在一个部署会导致停机的环境中工作，这是一个早上很早就要做的事情，因此它们会对尽可能少的最终用户产生影响。因为它们必须在清晨发生，所以发生的频率不高。
结果，多个特性被收集和发布在一起，部署自动变得紧张起来。

我当时工作的后端应用程序为登录的用户维护会话。如果其中一个服务器出现故障，存储在该服务器上的用户会话将会丢失。在这种情况下，你不能在中午发布一个 bug 补丁，因为你可能会让部分用户退出。

首先，我认为 JWTs 是这个问题的解决方案。这个问题还有其他可能更好的解决方案，但我们暂时忽略这些。)
同一个后端应用程序的两个或多个实例可以位于负载平衡器之后，并向客户端发出 jwt。
所有这些实例都能够验证由其中任何一个发出的 jwt。
每个 JWT 的主体可以包含通常存储在会话中的信息，比如当前登录用户的标识符。
如果其中一个实例关闭(例如，在部署期间)，负载均衡器会将请求路由到剩余的实例，而客户端不会注意到任何事情。

我确信 JWTs 可以解决我的一个问题，但是我不确定客户机和服务器应该如何交换它们。它们应该在报头中与请求一起发送，还是应该保存在 cookie 中？
对于后端应用程序之间的通信，答案很清楚。
遵循惯例并把它们放在标题中要容易得多，而且把它们放在 cookies 中也没有什么好处。
对于在浏览器中运行的客户端应用程序和后端应用程序之间的通信，答案不太清楚。
我记得在准备我的演讲时疯狂地搜索最佳实践，面对各种矛盾的说法和建议。在我们讨论我当时得出的结论之前，我们需要绕道而行。

## CSRF 和 XSS

术语“跨站点请求伪造(CSRF)”用于这样一种情况:由于上次访问留下的 cookies 仍然存在，其他人的 web 应用程序秘密地让其访问者对您的 web 应用程序执行操作。

下面的例子(由 [OWASP](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 提供的一个例子的修改版本)显示了一个欺骗不知情的用户发送 10.000 欧元(？)到我在 bank.com 的银行账户:

```
<form action="http://bank.com/transfer.do" method="POST">
  <input type="hidden" name="account" value="LUC"/>
  <input type="hidden" name="amount" value="100000"/>
  <input type="submit" value="View my pictures"/>
</form> 
```

术语“跨站点脚本(XSS)”用于这样的情况，即有人能够将其脚本作为您的 web 应用程序的一部分来执行。

下面的例子(直接从 [OWASP](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS)) 剽窃而来)展示了 JSP 模板的一部分，它允许任何人在相应的网页上执行代码:

```
<% String eid = request.getParameter("eid"); %>
    ...
    Employee ID: <%= eid %> 
```

想象一下点击[http://example.com/employee.jsp?后你会做的噩梦 Eid = alert % 28% 22 you % 20 have % 20 been % 20 powned % 22% 29](http://example.com/employee.jsp?eid=alert%28%22you%20have%20been%20p0wned%22%29)...

## Cookie 还是 header？

如果你把你的 jwt 放在饼干里，你需要采取预防措施来对抗 CSRF。如果你使用安全的、只有 HTTP 的 cookie，你就不需要担心 XSS，因为脚本无法访问这些 cookie 的内容。没有人可以滥用 XSS，用你的 JWT 来冒充你。

如果你把你的 jwt 放在一个头里，你就不需要担心 CSRF。然而，你确实需要担心 XSS。如果有人可以滥用 XSS 窃取你的 JWT，这个人就可以冒充你。

在我 2016 年的报告中，我指出“防御 CSRF 是简单而持久的。”
这个声明是基于[开放网络应用安全项目(OWASP)](https://www.owasp.org/) 当时提出的建议。几年后，对 CSRF 的防御仍然是持久的，但没那么简单了。
我们一会儿再回到这个话题。

另一方面，XSS 是你需要时刻牢记在心的东西。你添加的每一个模板都可能为 XSS 带来新的机遇。
这同样适用于您直接或间接添加到前端项目中的所有 NPM 包。

我由此得出的结论是，jwt 属于一个安全的、仅支持 HTTP 的 cookie，应该与针对 CSRF 的预防措施结合使用。

## 眼见为实

因为布丁的味道要靠吃来验证，所以我编写了一个简单的前端应用程序和两个后端应用程序，演示了基于会话和基于 JWT 的身份验证方法:[https://github.com/ljpengelen/java-meetup-jwt](https://github.com/ljpengelen/java-meetup-jwt)

通过一个简单的`docker-compose`命令，您可以启动两个后端中任何一个的三个实例、一个数据库和一个服务于前端并充当负载平衡器的 nginx 实例。
你可以在浏览器中打开前端，创建一个账户，登录，然后用`docker stop`停止一些后端实例。

对于基于 JWT 的后端，停止哪两个实例并不重要。
在基于会话的后端的情况下，停止您连接到的实例将终止您的会话。

# 对抗 CSRF 的措施

OWASP 有一份针对 CSRF 的措施的备忘单。
上面提到的应用程序使用了其中的两种方法。

首先，他们通过检查`Origin`和`Referer`报头来对抗 CSRF。
如果这些头的值都不匹配给定请求的期望值，则请求被拒绝。

其次，后端返回的每个响应在两个位置包含一个安全的随机令牌。一个在报头中发送，前端可以读取。
另一个存储在会话(在基于会话的后端的情况下)或另一个安全的、仅 HTTP 的 cookie(在基于 JWT 的后端的情况下)中，并且仅可由后端访问。
这些令牌由密码安全的随机数生成器生成。
前端应用程序读取每个响应报头中的令牌，并将其与下一个请求一起传递。
对于发送到受保护端点的每个请求，后端会检查这两个令牌是否匹配。
如果匹配，请求将被批准。
否则，否定。

在前端跟踪 CSRF 令牌并不完全简单。跟踪最新的令牌值并在每次请求时转发它需要一点努力，但如果你问我，这是一个可以接受的代价。

对于基于 JWT 的后端，上述两个措施都来自于 [OWASP 备忘单](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)中描述深度防御措施的部分。
第二个措施被称为双重提交 cookie 技术。
为了减轻该技术的已知问题，CSRF 令牌被存储在 JWT 中。
此外，账户标识符也包含在登录用户的 JWT 中。
将 CSRF 令牌存储在 JWT 中使得后端应用程序能够验证它自己产生了令牌。将 CSRF 令牌与帐户标识符结合起来，使得攻击者无法为另一个用户重用令牌，即使他们能够替换 cookies。

## JWT 的寿命

考虑一下以下情况:当您更改凭证时，已经发布的 jwt 会发生什么情况？删除帐户后，已经发行的 jwt 会怎样？
在这两种情况下，现有的 jwt 将保持有效。
如果没有额外的措施，jwt 将保持有效，直到它们过期或者服务器上的秘密被更改。如果有人得到了令牌，它就可能被滥用，直到过期。
如果你想通过改变服务器上的密码来使一个令牌失效，你可以使所有的令牌失效。

JWT 什么时候到期？一方面，它们应该尽快过期，以防止长期误用。
另一方面，它们应该尽可能晚地过期，这样用户就不用一直重新认证了。

在实践中，两种类型的令牌一起使用，以实现两全其美。
短期*访问*令牌用于每个请求的认证。
长寿命*刷新*令牌用于在需要时生成新的访问令牌。

每次使用刷新令牌来获得新的访问令牌时，可以进行一些额外的检查来增强安全性。例如，刷新令牌可以与黑名单结合使用，以使在给定时间点之前为特定用户发布的令牌无效。

## 这是在保护你免受哪种虐待？

因为 jwt 存储在安全的、仅支持 HTTP 的 cookies 中，所以很难想象有人能够自己访问 jwt。例如，攻击者需要访问受害者的计算机来读取这些 cookies 的值。
上述黑名单可用于使这样构成的 jwt 无效。
然而，如果有人能够直接从你的电脑上访问 cookies，你就有更大的问题要担心，这超出了应用程序开发者的责任。
此外，对于那些愿意将你的生活变成昆汀·塔伦蒂诺电影来获取你的数据或凭证的人，没有合理的防御措施。

攻击者能够读取 jwt 值的其他情况是，攻击者能够截获客户端和服务器之间的流量，或者攻击者能够访问服务器。在这种情况下，所能做的就是修补安全漏洞，并更改用于签署 jwt 的密钥。
后者是使之前发布的所有 jwt 无效的最简单的方法。
针对这些类型攻击的防护无法在应用程序级别实现。

简而言之，您的 jwt 在它们的 cookies 中是相当安全的。然而，更现实的情况是，你可能无意中在你的应用中引入了一个 XSS 漏洞。
这使得攻击者能够访问 CSRF 令牌的值，并在 CSRF 攻击中使用它。
同样在这种情况下，你所能做的就是在修补漏洞后更改密码使所有令牌失效。

## 结论

我不是安全专家，我必须强调，在这个问题上，你不应该把我的建议错当成绝对真理。相反，我希望这篇文章能让你理解我的推理，并帮助你在不同形式的认证之间做出明智的决定。

我很清楚几年前我遇到的矛盾的建议仍然存在，大多数人把他们的 jwt 放在一个标题中。我猜那些人更害怕 CSRF，而我更害怕 XSS。

## *补遗*

这篇博文发表后，我的同事卢克·范登博恩(T1)分享了一篇关于“T2”用 cookie 前缀保护 cookie(T3)的文章。
巧合的是，这篇文章描述了一种修补 JWT 后端安全漏洞的方法。
这个后端容易受到名为[登录 CSRF](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html#login-csrf) 的攻击，即攻击者能够让用户使用攻击者的帐户登录。
当攻击者能够访问托管您的应用程序的域的不安全子域时，这种攻击是可能的。攻击者可以使用这个不安全的子域为保存 CSRF 令牌的 cookie 设置任意值。
这种攻击只可能针对用于登录的 API 调用，因为 CSRF 令牌在登录后绑定到用户的帐户标识符。

只需在保存 CSRF 令牌的 cookie 名称前添加前缀`__Host-`就能触发[浏览器行为，缓解这种类型的攻击](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#Directives)，至少对 Chrome 和 Firefox 的用户来说是如此。