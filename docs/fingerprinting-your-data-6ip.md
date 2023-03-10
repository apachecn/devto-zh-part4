# 指纹识别您的数据

> 原文：<https://dev.to/caffiendkitten/fingerprinting-your-data-6ip>

[![](img/c0a2ef222250ae799f51392548403418.png)](https://i.giphy.com/media/l0HlN5WuWRBciFOJW/giphy.gif)

人们每天都使用一种设备来浏览他们的电子邮件、新闻或社交媒体，在这些数字冒险中，我们被跟踪着。我们的计算机浏览器和设备需要知道一些关于我们的特定信息，以特定的方式呈现页面，从而在所有设备上创建单一的用户体验。然而，他们不“需要”保存或跟踪这些数据，因此，他们创建了我们的数字“指纹”，或个人资料。

## 什么是‘浏览器指纹识别’？

虽然浏览器指纹最初是由安全专家使用的，但它是从用户/用户的数字体验中收集数据并将其编译成关于他们的个人资料的过程。这些可以是诸如屏幕大小、位置、在一个页面上花费的时间，甚至是在改变页面或关闭设备屏幕之前的滚动量。所有这些以及更多的动作可以用来使用户体验更好，使广告与用户或用户的一般数据收集/跟踪更相关。像 Youtube 这样的大公司使用这些数据来跟踪用户以及他们如何在网站上移动，并确定让他们在网站上停留更长时间或让他们点击广告的最佳方式。

## 指纹数据是如何收集的？

使用加密“哈希”函数将收集的属性数组压缩成一个较短的 ID 号，该函数只在本地执行，不会离开您的设备。然后，网站上的 JavaScript 用于检测您的特定设备的信息。您的“用户代理”指纹用于识别您正在使用的浏览器和平台。
你将在“用户代理”信息中看到的两个有趣的数据点与“封面动画”有关，分别是“字体列表”和“画布散列”。“前者是您已经安装在计算机上的字体列表。浏览器需要访问你的字体，以便在你的屏幕上显示文本，但是因为用户经常在他们的设备上添加默认的字体列表，这可以成为在线识别你的特别有效的方法。“画布散列”也许是最独特的特征。开发人员使用 HTML5 画布通过 JavaScript 在浏览器中绘制 2D 和 3D 图形。”(3)

[![](img/09788e4f5fdae38289d9b840574c98a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GXymtn8S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uytjwwh5ca8yolwtdpyy.jpeg)

## 能否预防，如何预防？

Mozila 的 Nick Briz 说,“一旦你的数字指纹被组合起来，它就永远是准确的。随着跨浏览器指纹识别技术的发展，这种技术能够在 99%的情况下成功识别用户。这意味着，即使你采用了多种推荐的隐私保护措施(通过 VPN 屏蔽你的 IP 地址，删除或阻止 cookie)，追踪器仍然可以在你访问网站时使用你的数字指纹来重新识别和重新 cookie 你的设备。”(3)

## GDPR 和数字指纹

随着《一般数据保护条例》( GDPR)的实施，公众对自己的个人数据有了更多的了解，因为 GDPR 希望保护任何可能与可识别个人相关的个人数据。“这个定义不仅涵盖了所有种类的在线标识符(如您的计算机的 MAC 地址、您的网络的 IP 地址或 cookie 中的广告用户 ID)，还涵盖了一些不太具体的特征，包括指纹识别所依赖的浏览器特征组合。"(6)
遗憾的是，这似乎并没有完全阻止数字指纹，因为每个处理个人数据的实体都可以简单地证明他们有理由合法地这样做...或者让用户选择要跟踪的 cookies。

## 依我看...

[![](img/88127ac60feec822fca5fe4a61048408.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OqW7qzAg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yaaqyv75ev48zaofjmhl.png) 
这是我自己来自 Amiunique.org 的指纹。正如它显示的那样，我目前在一台装有 Chrome 浏览器的 MAC 电脑上登录。这些都是常用的东西，但是在我写这篇文章的时候，由于我的使用和特殊的添加扩展，我的“完整指纹在目前收集的 1083878 个指纹中是独一无二的。”(5)
在我看来，我并不在乎我的日常使用是否被跟踪。我知道这正在发生，我越是融入人群...越好。我向世界展示的数据越不独特，越不像是应该详细查看的东西，我就越开心...暂时如此。

#### 资源

1.  [设备指纹表单维基百科](https://en.wikipedia.org/wiki/Device_fingerprint)

2.  [水线数据](https://www.waterlinedata.com/blog/data-fingerprinting-the-magic-is-finally-revealed/)

3.  [这是你的数字指纹](https://blog.mozilla.org/internetcitizen/2018/07/26/this-is-your-digital-fingerprint/)

4.  [在这里找到了信息图](https://www.cshub.com/)

5.  [检查你自己的“唯一性”](https://amiunique.org/)

6.  [GDPR 和浏览器指纹:它如何改变最狡猾的网络追踪者的游戏](https://www.eff.org/deeplinks/2018/06/gdpr-and-browser-fingerprinting-how-it-changes-game-sneakiest-web-trackers)

##### 请注意，我是学生，还在学习。如果我说的不正确，请告诉我。我很想了解更多我可能不完全了解的东西。