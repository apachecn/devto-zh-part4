# 本地存储与会话存储

> 原文：<https://dev.to/caffiendkitten/localstorage-vs-sessionstorage-f9k>

在我为熨斗学校做最后一个项目时，我发现了一个我被告知要使用的本地存储的安全问题。

因此，刷新“类似于“sessionStorage”，localStorage 是一个只读属性，允许访问“文档起源的存储对象；存储的数据跨浏览器会话保存。"(5)注意:存储在 localStorage 中的数据没有到期时间，因此如果需要，必须设置它。

在我的例子中，当用户登录时，我需要对 JWT 对象的 localStorage 对象使用 setItem 方法，并将其保存在浏览器中，然后在用户注销后清除 JWT 对象的 localStorage。这将允许清除和重置所有与用户相关的状态，这样其他人就无法访问该站点并使用他们的登录凭据。" (1)

* * *

## 剧情转折时间！！

问题在于 localStorage 如何存储信息。像许多网站一样，当你关闭一个标签或窗口时，浏览器会保留一些会话信息，这样你就不需要每次导航到一个网站时都登录。使用 localStorage，该信息没有超时设置，因此，用户信息永远不会离开浏览器。这似乎是一个好主意，这样用户就不需要记住更多的登录信息，但是如果你正在开发一个网站，最好每隔一段时间强制退出，以确保用户数据更加安全。

## 然而，本地存储是如何引起问题的呢？

嗯，“如果攻击者可以在你的网站上运行 JavaScript，他们可以检索你存储在本地存储中的所有数据，并将其发送到他们自己的域中。这意味着您在本地存储中获得的任何敏感信息(如用户的会话数据)都可能受到威胁。”(2)

## **这就是 sessionStorage 成为更好选择的地方。**

虽然 sessionStorage 属性也像 localStorage 一样允许在 web 浏览器中使用键/值对，但 sessionStorage 是比 localStorage 更好的选择，因为当浏览器选项卡关闭时，会话数据会被清除。这很重要，因为...如果有人可以获得您的 JWT 副本，他们就可以代表您提出请求。这很糟糕。

## 什么时候用？

任何时候，如果你有不想在社交媒体网站上分享的信息，比如...JWT、用户名、密码、信用卡信息 API 密钥、AWS 凭证或个人身份信息。实际上，这个列表还可以继续下去，但是作为一个开发者，这些是使用 sessionStorage 要考虑的主要事情。

## 语法差异？没有。

localStorage 和 sessionStorage 在存储中创建对象、删除对象和访问对象的语法基本相同。

[![Alt Text](img/9bd05c2cd8339201e016f0c835063ca6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NaDqROqp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ka3mzbyh2psgab7joq43.png)
[![Alt Text](img/6b1347486017921f75ae0d511e94fd5f.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--vCtTgpTp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a7mf8fqfxfm991cjroy3.png)

## 用哪个？？？

这取决于您存储的信息。如果你正在构建一个静态的单页应用程序，比如我的 Flatiron 学校项目，使用 localStorage 意味着该网站可以独立于任何 web 服务器运行，这很酷，但我的项目专注于安全性，如果你正在构建任何将要处理任何敏感信息的东西，那么使用 sessionStorage。而且，如果您确实为一个简单的单页应用程序使用了 localStorage，它只是给出信息而不存储任何东西，那么您可以使用 localStorage，或者将 localStorage 设置为在“x”时间后超时。
[![Alt Text](img/5c88a89f2f5b791d041e847c09a3c646.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--DRMiRbrV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lqtamw5q6xckxmrvn3ld.png)

对于我的项目，我决定使用 sessionStorage，因为这是一种更安全的方式，而且除了将所有的“localStorage”更改为“sessionStorage”之外，它不需要任何额外的东西。现在，当我关闭正在工作的选项卡时，它会自动将我注销。浏览器可以设置为存储密码，所以我宁愿使用浏览器中的密码管理器来记住这些登录凭证，也不愿让 localStorage 对象保留它不需要的信息。

#### 参考文献

1.  https://dev . to/caffiendkitten/understanding-password-managers-and-my-attempt-at-building-one-49oh-temp-slug-1564498？preview = 316495 e 87990 a 59 a5e 714 DBC 40 DD 36199021111 AE 4756 e 209 a 43 CB 1b 049172177971 FB 47 c 82596 cdd 383 FB 466339103 b 2615 c 35 f 925 b 354 f 23 a1 f18 d
2.  [https://dev.to/rdegges/please-stop-using-local-storage-1i04](https://dev.to/rdegges/please-stop-using-local-storage-1i04)
3.  [https://developer . Mozilla . org/en-US/docs/Web/API/Window/session storage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage)
4.  [https://gist . github . com/shai k2 many/039 A8 EFE 13 dcafb 4 a3 ffc 4 e 5 FB 1 dad 97](https://gist.github.com/shaik2many/039a8efe13dcafb4a3ffc4e5fb1dad97)
5.  [https://developer . Mozilla . org/en-US/docs/Web/API/Window/local storage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)
6.  [https://www . taniarascia . com/how-to-use-local-storage-with-JavaScript/](https://www.taniarascia.com/how-to-use-local-storage-with-javascript/)

###### 请注意，我是学生，还在学习。如果我说的不正确，请告诉我。我很想了解更多我可能不完全了解的东西。