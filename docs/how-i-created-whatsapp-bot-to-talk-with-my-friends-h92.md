# 我是如何创建 Whatsapp bot 来与朋友交谈的？

> 原文：<https://dev.to/arpitvasani/how-i-created-whatsapp-bot-to-talk-with-my-friends-h92>

# 索引

*   [🤔为什么？](#why)
*   [🤨怎么会？](#how)
    *   我是如何获得灵感的？
    *   我是怎么做到的？
    *   [给我看看代码](#enough-show-me-the-code)
    *   [最终结果](#final-results)
*   [✌的结论](#conclusion)

### 🤔为什么？

这个标题似乎有点粗鲁和懒惰。对吗？但是如果你想想我的处境，你就会明白。在印度，社交比活着更重要。如果你不在几个群里回复或者发几条*【早上好】*的消息，别人会以为你其实已经死了。还嘲讽你很忙没时间陪他们。😤

现在，我和社交媒体的问题是，我想在我的时间里使用它，而不是在他们给我发通知的时候。所以，有些情况下，我会在 4-5 天后回复一个帖子，但那时我的回复已经无关紧要了，因为那个时刻已经过去了。我们考虑一下发帖吧。例如生日纪念日等...但问题是我不想成为那种 4-5 天后才回复的人。我们都有 24 小时，我尽我所能把它分配给我所能支配的所有事情和优先事项。如果一周后有人回复我，我会很失望，所以我不想成为他们的那个人。

所以，我想出了我的编程技巧来解决/自动化这个场景，这将节省我的屏幕时间。🤩

### 🤨怎么会？

#### 我是怎么得到灵感的？

我希望在 web 上实现自动化，就像 Google 的顶级结果一样，Selenium 出现在我的脑海中，但是作为一个 JavaScript 爱好者，我选择 JS 作为我的盟友。事实上，我在 npm 中发现了几个针对 Selenium 的模块，但并没有留下深刻的印象😐。

在搜索的时候，我偶然发现了来自 google IO 2018 的视频和[这个视频](https://www.youtube.com/watch?v=VDGiQ2cwFP4&feature=youtu.be&t=500)，这让我想到

[![this](img/00ba423ce6ef8314432ebad749b5dca5.png)](https://i.giphy.com/media/BmmfETghGOPrW/giphy.gif)

#### 我是怎么做到的？

首先，我从这篇文章中了解了一些技巧

[![hardkoded](img/2ccb2b9517de14b671eb5f46e12cfbc7.png)](/hardkoded) [## 用木偶戏制作 WhatsApp 机器人

### Dario Kondratiuk 2019 年 4 月 12 日 7 分钟阅读

#csharp #puppeteersharp](/hardkoded/creating-a-whatsapp-bot-with-puppeteer-sharp-16f2)
then, I realized that relying on DOM is not a good idea🤨. What if classes or selectors I have used got updated in the page🤔? So I decided not to use DOM. (But I ended up using it any way for other feature🤷‍♂️. Will write about it in another post. Stay Tuned.......😎)

就像一个生产问题在截止日期前得到解决😏，我发现了一个回购协议，它提供了对 WhatsApp 的可编程访问，其工作方式与我想要的一样。

#### 够了给我看看代码

以下是总体步骤和要点。我还添加了一个回购的链接。

1.  首先，我创建了一个木偶师浏览器窗口，然后像这样打开 Whatsapp

[![code screenshot](img/8afde550c48e6153524583c4154929be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AwDFcHzY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4s02h7ha9fw1r1wf7ke9.png)

1.  然后，我创建了一个 json 文件来管理消息及其等效的回复，还添加了一些配置。

[![code screenshot](img/62d885390135f2e2c1e95de851c05763.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R9Kq6tyD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a0wbjm0p3gjujrj1n86h.png)

1.  接下来，我在`inject.js`中添加了一个函数，每次我收到新消息时都会调用这个函数

[![code screenshot](img/5e7bd8a65f88d387ecf3006feedae108.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1MMU_-mb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/urz4ixk1iud6siyczx5o.png)

#### 最终结果

上面还有一些库，如 [Ora](https://www.npmjs.com/package/ora) 、 [Cli-progress](https://www.npmjs.com/package/cli-progress) 和 [qrcode-terminal](https://www.npmjs.com/package/qrcode-terminal)

[![Garnishing giphy meme](img/8a475045cbc49a5e6f3c84c0be4c966c.png)](https://i.giphy.com/media/l4Jz3a8jO92crUlWM/giphy-tumblr.gif)

我得到了这个

[![preview of wbot](img/eb01ab4e551367af5d6b9c58d05aefbe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MaU69g2Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/al5vwx3nso5pfhod3n0r.gif)

这是完整的代码

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [左勾拳”](https://github.com/vasani-arpit) / [中刺](https://github.com/vasani-arpit/WBOT)

### NodeJS 中的 WhatsApp 的一个简单的基于 Web 的 BOT😜。工作截止日期📅2021 年 12 月 9 日

<article class="markdown-body entry-content container-lg" itemprop="text">

# WBOT

[![Support me on Patreon](img/f760e2011ab3986aae07c8bd3630cd11.png)](https://www.patreon.com/arpit_vasani)[![Buy me a book](img/d6e2c553b5f39bc0d49f5fe97962f3b9.png)](http://amzn.in/iCUjhKZ)[![PayPal](img/541bb194107b95696560db5e47ac71c8.png)](https://www.paypal.me/arpitvasani)

> 一个用于 whatsapp web 的简单 Nodejs 机器人

[预览](https://github.com/vasani-arpit/WBOT#preview)[特色](https://github.com/vasani-arpit/WBOT#features)[下载](https://github.com/vasani-arpit/WBOT#downloads)[如何？](https://github.com/vasani-arpit/WBOT#how-to-start-the-bot)[采用的技术](https://github.com/vasani-arpit/WBOT#technologies)[为什么？](https://github.com/vasani-arpit/WBOT#why)[目标](https://github.com/vasani-arpit/WBOT#goals)[常见问题](https://github.com/vasani-arpit/WBOT#faq)

## ✨2021 年 3 月 30 日更新

作为一个小项目，WBOT 已经成为在 whatsapp 上经营业务的小企业主的必备工具。WBOT 通过节省时间和资源，帮助他们简化业务过渡。我被所有人通过电子邮件和电报给我的支持所淹没。我会继续更新 WBOT。

谢谢<g-emoji class="g-emoji" alias="pray" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f64f.png">🙏</g-emoji>

## <g-emoji class="g-emoji" alias="mag" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f50d.png">🔍</g-emoji>预览

### 快速预览

[![Screenshot gif](img/12dc705142576fcba3dd9c54d07a1a0d.png)](https://user-images.githubusercontent.com/6497827/58411958-1dcc8000-8093-11e9-8aeb-5747efe10266.gif)

## <g-emoji class="g-emoji" alias="zap" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a1.png">⚡</g-emoji> 特色

*   <g-emoji class="g-emoji" alias="art" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f3a8.png">🎨</g-emoji>高度可定制的 json
*   <g-emoji class="g-emoji" alias="100" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4af.png">💯</g-emoji>完全免费供个人使用
*   <g-emoji class="g-emoji" alias="lock" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f512.png">🔒</g-emoji>完全隐私。您的数据将永远伴随着您
*   <g-emoji class="g-emoji" alias="computer" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4bb.png">💻</g-emoji>自动下载媒体文件
*   <g-emoji class="g-emoji" alias="busts_in_silhouette" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f465.png">👥</g-emoji>多个实例

## <g-emoji class="g-emoji" alias="arrow_down" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b07.png">⬇</g-emoji> 下载量 <g-emoji class="g-emoji" alias="arrow_down" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b07.png">⬇</g-emoji>

| 马科斯 | Windows 操作系统 | Linux 操作系统 |
| --- | --- | --- |
| [下载最新版本](https://github.com/vasani-arpit/WBOT/releases/latest/download/WBOT-mac.zip) | [下载最新版本](https://github.com/vasani-arpit/WBOT/releases/latest/download/WBOT-win.zip) | [下载最新版本](https://github.com/vasani-arpit/WBOT/releases/latest/download/WBOT-linux.zip) |

## 支持的平台

Wbot 支持以下平台:

**macOS:** 支持的最低版本是 macOS 10.9。

**视窗:** …

</article>

[View on GitHub](https://github.com/vasani-arpit/WBOT)

### ✌的结论

这就是技术的意义所在。识别问题，并使用工具/技术解决问题。不是吗？像 Google Puppeteer 和 NodeJS 这样的工具的组合有很多可能性。我们只需要有足够的自我意识来寻找解决我们日常生活中问题的方法。

* * *

！！用你的评论奖励我吧🎁.....你真棒！！

感谢 [starline](https://www.freepik.com/starline) 的背景向量

这是我第一次在互联网上分享/写作。请随意指出错误，以便我在下一篇文章中改进。