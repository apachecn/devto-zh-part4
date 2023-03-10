# Xss JaVaSCRipt 多语种语言

> 原文：<https://dev.to/caffiendkitten/xss-javascript-polyglots-4i64>

## 什么是多语言用户和 JavaScript 多语言用户？？

*   多语者是由不同语言的成分组成的人或物。
*   JavaScript Polyglot 是一个跨站点脚本(XSS)向量，它可以以原始形式在各种注入上下文中执行，或者是一段可以在应用程序的多个上下文中执行的代码。因此，一个 JavaScript 多语言用户可以同时拥有多种功能，比如 JavaScript/JPEG

[![](img/36f28eec39f7f3ee6038e2a76e6656f6.png)](https://i.giphy.com/media/26u4m61P3jiZlc8aA/giphy.gif)

## 为什么要多语？

使用多语言的最大原因是绕过浏览器的内容安全策略(CSP)。内容安全策略(CSP)是附加的安全层，有助于检测和缓解某些类型的攻击，包括跨站点脚本(XSS)和数据注入攻击(4)

## 多语种测试 XSS 有效载荷。

所以！！下面是一个多语言的例子，但是你会注意到 Dev.to 对它做了一点修改，因为我输入的这两个字符串是一样的。酷吧？他们似乎很注意。=)
有点难看的字符串对吧？但是正如你所看到的，里面有 HTML 标签试图引起一些问题。此外，Javascript 这个词的大小写很奇怪，试图绕过 CSP。很明显没用。

jaVasCript:/*->

```
jaVasCript:/*--></style></textarea></script></xmp> 
```

Enter fullscreen mode Exit fullscreen mode

这个测试将在多个上下文中执行，包括 html、脚本字符串、js 和 url。(5)

## 如何才能做一个 Javascript 多语者？

[![](img/1ffc501e8bef4ee099948763f914893d.png) ](https://camo.githubusercontent.com/217e6a698b61bd9b2cb98ee64803627b61d95a88/68747470733a2f2f7062732e7477696d672e636f6d2f6d656469612f4457694c6b335558344145306a4a732e6a7067) (6)

*   jaVasCript::ECMAScript 中的一个标签；否则就是 URI 计划。
*   /*-/*/`/*\`/*'/*"/* */:ECMAScript 中的多行注释；文字分隔符序列。
*   (/* */oNcliCk=alert()):一个缠绕在调用括号中的执行区！
*   //% 0D % 0A % 0D % 0A//:ECMAScript 中的单行注释；HTTP 响应头中的双 CRLF。
*   :一个狡猾的 HTML-tag-breaker 序列。
*   \x3csVg/\x3e:一个无害的 sVg 元素。

*多语言的总长度为 144 个字符。* (2)

## 图像多语和 Javascript

下面是一个多语言图像的例子。它看起来像一个正常的图像，但原始元数据已经被改变。参考第二个图像，查看他们将图像注释更改为什么的细节。老实说，我并不完全理解它，但它可以被改变和使用，这很有趣。
如果图像保存不当，该代码可用于在多语言环境下运行警报。见文末防止多语者的方法，查看关于图像的存储。
[![](img/132158fc37e6c04bf87ee159174f0e32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LHRq7QZy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://portswigger-labs.net/polyglot/jpeg/xss.jpg) 
下面的代码是将图片作为 JavaScript 执行

```
<script charset="ISO-8859-1" src="http://portswigger-labs.net/polyglot/jpeg/xss.jpg"></script> 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/d4007cfff23df8d7e314d5df26a24230.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3s2N_1OQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cirwfwzhyo8ih1d6x83h.png)

## 多语者的危害

所以，就我的理解，假设你有一个接受图片的网站，比如 Instagram，你允许用户上传一般/JPEG 格式的图片。如果这些上传与您的应用程序在同一个域中，并且您的 CSP 允许来自“自身”的脚本，您可以通过注入脚本并将其指向该图像，使用多语言 JPEG 来绕过 CSP。(3)

## 防多种语言

虽然不是最容易设置多语言攻击的攻击，但它仍然是一个需要防范的相关问题，因为许多新开发人员都依赖相同的后端和平台来开发新应用程序。如果一个依赖项变得过时或易受攻击，那么所有使用它的应用程序也会受到攻击。

避免多语言负载攻击的最佳方式是:

*   净化和验证任何用户输入
*   避免使用。innerHTML 标记从用户输入中获取信息
*   存储任何接收到的图像，与它们的路径和元数据分开，就像亚马逊 S3 桶。

#### 资源

1.  [https://www.merriam-webster.com/dictionary/polyglot](https://www.merriam-webster.com/dictionary/polyglot)
2.  [https://github . com/0x sobky/hack vault/wiki/Unleashing-an-Ultimate-XSS-Polyglot？source=post_page -](https://github.com/0xsobky/HackVault/wiki/Unleashing-an-Ultimate-XSS-Polyglot?source=post_page---------------------------)
3.  [https://portswigger . net/blog/bypassing-CSP-using-polyglot-JPEG](https://portswigger.net/blog/bypassing-csp-using-polyglot-jpegs)
4.  [https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)
5.  [https://www . owasp . org/index . PHP/XSS _ 过滤 _ 规避 _ 作弊 _ 工作表# XSS _ 定位器 _.28Polygot.29](https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet#XSS_Locator_.28Polygot.29)
6.  [https://github . com/swisskyrepo/payloads all the things/tree/master/XSS % 20 injection # polyglot-XSS](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection#polyglot-xss)

###### 请注意，我是学生，还在学习。如果我说的不正确，请告诉我。我很想了解更多我可能不完全了解的东西。