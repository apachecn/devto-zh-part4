# HTML 中的“href”与“src”

> 原文：<https://dev.to/dance2die/href-vs-src-in-html-10l7>

昨晚，我[发泄了我对`<link>`的不满](https://twitter.com/dance2die/status/1151286723122466816)要求`href`指向 CSS 样式表而不是`src`。

我会在 Twitter 上分享我得到的帮助和美好的人们提供的历史， [Vuild](https://vuild.com/) ，[Brendan Eich](https://brendaneich.com/)(JavaScript 的创造者)，和[hkon Wium Lie](https://www.wiumlie.no/en.html)(CSS 的创造者)。

## T1；速度三角形定位法(dead reckoning)

`href`资源基于触发器加载，而`src`资源自动加载。

不是 100%准确，因为 CSS 文件会自动加载。

`href`和`src`的混淆是由于早期 web 标准时代的历史原因。

## 【href】&【src】的区别

[Vuild](https://twitter.com/vuildco) 回答说`src`是一个请求，而`href`是一个资源的链接。

> ![Vuild profile image](img/842761b6156fda1c2ec72c19b7099175.png)Vuild@ vuildco![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ dance 2 die](https://twitter.com/dance2die)一个是链接(指向资源)，一个是请求源。
> 
> <a href = " https://image . jpg "><img src = " https://image . jpg "/></a>将嵌入(请求 src) &链接到它。它们是不同的东西。
> 
> CSS 是 src 请求。
> 
> 我在这里两样都做:
> [vuild.com/face](https://t.co/oq4TnIhLwL)00:33AM-2019 年 7 月 17 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1151288830860976128)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1151288830860976128)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1151288830860976128)2

由于无法把握其中的区别，我回答说“CSS”是下载/解析/应用的请求，应该是使用`src`。因为 Vuild 也有同感，他[请求](https://mobile.twitter.com/vuildco/status/1151291872322760705) [布伦丹·艾奇](https://mobile.twitter.com/BrendanEich)和[霍肯·维姆·列](https://mobile.twitter.com/wiumlie)帮忙。

Brendan Eich 友好地回答说,`src`是“自动包含”,而`href`ed 资源是在动作发生后加载的。

> ![BrendanEich profile image](img/c27772ea4c4766f007962adbd8fdd98a.png)布伦丹内克@布伦丹内克![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ dance 2 die](https://twitter.com/dance2die)[@ vuildco](https://twitter.com/vuildco)[@ wiumlie](https://twitter.com/wiumlie)[@ brave](https://twitter.com/brave)src 也在 img 上，是为了加载嵌入；href 用于超文本引用，在用户点击元素之前不会加载 2019 年 7 月 02:28 AM - 17 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1151317825908166656)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1151317825908166656)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1151317825908166656)2

> ![BrendanEich profile image](img/c27772ea4c4766f007962adbd8fdd98a.png)Brenda neich@ Brenda neich![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ dance 2 die](https://twitter.com/dance2die)[@ vuildco](https://twitter.com/vuildco)[@ wiumlie](https://twitter.com/wiumlie)[@ brave](https://twitter.com/brave)我的“嵌入”指的是自动包含 vs 点击加载，而不是内联 via out of line 也就是你注意到的大致正交。2019 年 7 月 17 日上午 02:52[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1151323779131305984)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1151323779131305984)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1151323779131305984)2

> ![BrendanEich profile image](img/c27772ea4c4766f007962adbd8fdd98a.png)布伦丹内克@布伦丹内克![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ dance 2 die](https://twitter.com/dance2die)[@ vuildco](https://twitter.com/vuildco)[@ wiumlie](https://twitter.com/wiumlie)[@ brave](https://twitter.com/brave)(vs not via)2019 年 7 月 03 日上午 04 点[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1151326755749163008)

但是对于 CSS，他同意`href`对于 CSS“[似乎【ed】与](https://twitter.com/BrendanEich/status/1151324101157347328)不一致”。

## 链接 href

kon Wium Lie 友好地提供了它的历史(如何)和背后的基本原理(为什么)。

基本原理是因为“HTML2 规范是这么说的”。

> ![Håkon Wium Lie profile image](img/d8d8fc0d928e1c80887f04ee8192a596.png)霍肯维姆列@维姆列![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ vuildco](https://twitter.com/vuildco)[@ dance 2 die](https://twitter.com/dance2die)[@ brendane ich](https://twitter.com/BrendanEich)[@ brave](https://twitter.com/brave)CSS 使用<链接 href...>因为 HTML2 规范是这么说的。我也发现自己在打 src。我们可以在那里节省一个字节！
> 【ietf.org/rfc/rfc1866.txt】2019 年 7 月 17 日上午 11:48[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1151458530567831553)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1151458530567831553)7[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1151458530567831553)22

> *[*有了，HTML2 spec。*](https://twitter.com/vuildco/status/1151462988798464002)*(Vuild 说出了我的话😀).*
> 
> *注意[是 HTML2 规范而不是 XHTML2](https://twitter.com/wiumlie/status/1151576065934012422) 。**

 *## img src

然后，kon 接着讲述了`img`如何以及为什么使用`src`。

`img.src`[提议](http://1997.webhistory.org/www.lists/www-talk.1993q1/0182.html)由[马克·安德森](https://twitter.com/pmarca)和[蒂姆·伯纳斯·李爵士](https://www.w3.org/People/Berners-Lee/)(万维网的发明者)赞成使用带有`href`的锚标签吗！*