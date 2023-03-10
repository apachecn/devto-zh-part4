# 无聊版的浏览器大战就要来了！

> 原文：<https://dev.to/koskila/the-boring-version-of-browser-wars-is-upon-us-b7c>

下一场浏览器战争即将来临，这一次它不是在专有 API 实现和糟糕实现的 CSS 和 JavaScript 标准的战场上进行，而是不同的生态系统与各自的 Blink 变种进行斗争，Blink 本身就是开源 Chromium 的一部分。

听起来很无聊，对吧？在某种程度上确实如此。没有火狐粉丝，没有 Opera 精英和互联网浏览器的普通人守在公共空间的各自角落，每个群体都俯视着其他群体。没有疯狂的公共服务，政府客户要求网站只能用 IE5 访问，所有其他浏览器都被屏蔽。也没有 ActiveX 或 Flash(谢天谢地)。

很多事情都变了，大部分是好的。但你知道什么永远不会变吗？

战争。战争永远不会改变。而且就算是浏览器大战也还是战争。我们仍然看到不同的党派在背后捅刀子，窃取创新，阻止其他人访问他们的网站(特别是谷歌正因此变得臭名昭著),一些最显眼的前沿人物也在推特上发表言论。这导致市场几乎被垄断，同时分裂成相互竞争的利基市场。

但是在深入之前，让我们看看我们是怎么到这里的，好吗？

## 孤独的堡垒已经陷落

自 2000 年代末至 2013-2014 年，浏览器市场出现了有趣的发展，当时长期停滞不前的市场(大多数人使用 Internet Explorer，一方面是因为它已经存在，另一方面是因为所有企业应用程序都需要它)迅速分裂为 Internet Explorer、Firefox 和 Chrome 三个几乎相等的大部分。

[![Google Chrome now accounts for close to 70% of web traffic (and Chromium variants of course way more) - close to the same percentage Microsoft held with Internet Explorer, when the market was at the most stagnant point.](img/667dd584acc5979a76dc5f8011360c2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YoEJ07bf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.koskila.net/wp-content/uploads/2019/06/StatCounter-browser-ww-quarterly-20091-20192-1024x576.png%3Fx79748)

谷歌 Chrome 现在占据了近 70%的网络流量(当然还有 Chromium 的变种),接近微软在 ie 浏览器市场最不景气时的比例。

[http://GS . statcounter . com/browser-market-share # quarterly-2009 01-2019 02](https://gs.statcounter.com/browser-market-share#quarterly-200901-201902)

但这并没有持续多久。当 IE 越来越变得无关紧要的时候，Chrome 慢慢占据了市场上的主导地位。

在微软转而使用 Chromium 来驱动他们唯一的浏览器 Edge 之后，基本上只剩下 Blink(Chromium 背后的浏览器引擎)、Gecko (Firefox)和 Webkit (Safari)了。在所有浏览器会话中，超过 80%在使用 Chrome——甚至更大一部分在使用 Blink。Gecko + Webkit 加起来只占全部使用的 13%。

好吧——也许 Trident 因其长寿而获得了(或不)荣誉称号——虽然它仍然支持 Internet Explorer，但没有人应该把它用作浏览器——它是传统应用程序的兼容工具，而不是网络浏览器。

这一发展似乎正在给我们带来一个新的、无聊的时代。在这个时代，几乎只有一种浏览器，以 10 种不同的方式打上烙印。类似的情况也在智能手机领域慢慢形成，在许多市场上，主要是安卓手机可用——三星有一款安卓手机，索尼有一款安卓手机，LG 有一款安卓手机，华为有一款安卓手机(至少目前是这样)，亚马逊有一款残缺的安卓手机，OPPO 有至少三款不同的手机。它们中的每一个都比运行“库存”Android 更糟糕，更具侵略性，因为如果每个人都以相似的价格销售相同的产品，你必须通过吞噬用户数据或接受垃圾软件供应商的贿赂来赚取额外的钱。

这是一个真正的柠檬市场。非常无聊和无趣的柠檬。

### 然而，这也不全是坏事

然而，所有这些乏味不仅仅是不好的。我的意思是，每个人都知道每个人都说中国人用什么来诅咒他们的敌人？

> "**愿你生活在有趣的时代**"
> 
> “中国诅咒”——这是 JFK 自己创造的。其实更像是“烂译者的诅咒”，不过现在先不去说这个。

有趣的时光不一定是美好的。事实上——我们在学校学到的关于世界大战的知识——那些绝对是有趣的时期，但不是生活的好时代！同样的，如果一定要有另一场浏览器大战(好吧，这是相当的比较，但你知道我的意思！)，我宁愿希望是无聊的那种。

对于一个开发者来说，只有一个浏览器的世界(基本上)并没有那么糟糕。好吧，只要那个浏览器是开发者喜欢的！但是支持的标准较少。

严肃地说，虽然任何市场的过度整合最终都会对几乎所有各方都不利，但至少我们仍然有像谷歌和微软这样的巨头在相互争斗，只是它们都有自己的 Chromium 变种！如果微软不得不参与另一波浏览器战争，我希望看到他们以一种相当有建设性的方式这样做——我认为我们现在正在 Edgium 身上看到这一点。

为什么我会这么想？嗯，最近微软和谷歌之间的沟通和一些尖锐的来回让我希望微软仍将致力于成为浏览器市场的一个大玩家——只是这一次，他们将专注于浏览器的创新，而不是在维护引擎时试图跟上不断发展的标准。因为他们现在终于有了一个尊重网络标准的引擎(至少比 Trident 更尊重)，他们可以花费昂贵的开发资源来引入时间线和网络笔记等功能，并有望比 Google 更好地优化实现:)

此外还有小玩家。

### 铬作为创新的催化剂？

维护良好且稳定的 Chromium 项目已经成为“另类浏览器”这个小而有趣的市场增长的催化剂。我在文章的最后列出了当前 Chromium 构建的替代浏览器的完整列表。

当引擎稳定时，更多的空间留给其他开发任务，因此即使较小的组织也可以实现自己的浏览器。这意味着有很多有趣的实现！

在所有的小型车中，我是勇敢的超级粉丝。在之前，我已经写过关于[他们的文章，但是他们的方法——一种比谷歌更友好的解决广告问题的方法——足够吸引人，值得再次简短提及。](https://www.koskila.net/?p=3302)

而且勇敢的绝对不止一个。虽然方法不同，但有许多有趣的项目。

> [进入铬叉的勇敢新世界！](https://www.koskila.net/brave-as-far-as-chromium-forks-go-it-is-batting-a-thousand/)

### 未来会怎样？

尽管我们将遭受浏览器市场停滞不前的痛苦，除非最终发生一些有意义的事情，但就目前而言，它至少让开发者的生活变得更容易，让小玩家有可能设计替代的浏览器实现，他们的创新发生在渲染之外——这是一件好事。

我们已经看到 Chromium 浏览器领域在限制 AdBlocking 扩展可以使用的 API 方面出现了分裂(例如，Opera、Brave 和 Vivaldi 将会找到解决办法，即使谷歌从 Chromium 中移除 API)。谷歌在 Chromium 领域的霸主地位有望被小众竞争对手慢慢削弱，也有望被微软和 Edgium 削弱。甚至还有[暗示](https://boingboing.net/2019/06/11/browser-wars.html/amp)的**实际**铬叉发生。

与此同时，让我们希望苹果能在 Safari 上做得更好，而 Mozilla 能在 Firefox 上继续他们有趣的工作 Firefox 现在被重新定位为一个注重隐私的浏览器。但我充满希望:即使大部分竞争发生在 Chromium 领域，只要谷歌不太占优势，创新仍有空间。

当谷歌对 Chromium 过于自信时，我们将会看到来自社区的抵制——也许是一群小众玩家在未来用竞争的叉子窃取 Chromium 开发的领导角色？

## 参考文献

这篇文章是在 koskila.net 的
[上的原始文章的基础上进行调整的。](https://www.koskila.net/the-boring-version-of-browser-wars-is-upon-us/)

原始参考:

*   网络浏览器使用统计
    *   [https://www.w3schools.com/browsers/](https://www.w3schools.com/browsers/)
*   铬叉列表
    *   [https://www . zdnet . com/pictures/all-the-chromium-based-browsers](https://www.zdnet.com/pictures/all-the-chromium-based-browsers)

[发微博吧！](https://twitter.com/intent/tweet?url=https://www.koskila.net/the-boring-version-of-browser-wars-is-upon-us/&via=koskila&text=The%20boring%20version%20of%20browser%20wars%20is%20upon%20us&hashtags=Brave%2CBrowserWars%2CMicrosoftEdge&ref_src=twsrc%5Etfw)

#### 安提·k·科斯凯拉的最新帖子([查看全部](https://www.koskila.net/author/koskila/))

*   [无聊版浏览器大战即将来临](https://www.koskila.net/the-boring-version-of-browser-wars-is-upon-us/)-2019 年 6 月 13 日
*   [扩展微软团队现已正式发布](https://www.koskila.net/extending-microsoft-teams-overview/)-2019 年 6 月 12 日
*   [如何解决“初始化应用程序时出错。错误:**找不到组件 id [guid]的清单。向团队添加 SPFx web 部件时。](https://www.koskila.net/how-to-resolve-error-initializing-application-error-manifest-not-found-for-component-id-guid-when-adding-an-spfx-webpart-to-teams/)-2019 年 6 月 4 日