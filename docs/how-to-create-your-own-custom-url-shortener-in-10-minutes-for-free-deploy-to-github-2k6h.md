# 如何在 10 分钟内免费创建您自己的自定义网址缩写器并部署到 GitHub🚀

> 原文：<https://dev.to/liyasthomas/how-to-create-your-own-custom-url-shortener-in-10-minutes-for-free-deploy-to-github-2k6h>

`sorry for the long post title - that's tldr;`

## T2】🔗mnml URL -支持自定义别名的现代 URL 缩写程序

*(最小 URL 的缩写🤦‍♂️)*

### 随时投稿上 **[GitHub](https://github.com/liyasthomas/mnmlurl)**

当我们其他人把网址缩写的晦涩难懂的东西复制粘贴到我们的 Twitter 账户上时，像《纽约时报》这样的网站正得意洋洋地使用“nyti.ms”这样的缩写网址发布链接。坦白地说，这让我有点嫉妒💁‍♂️

这就是为什么我用纯 JavaScript + HTML + CSS ❤创建了我自己的网址缩写器

我有没有提到这是完全免费和 100%开源的？是的。

mnml URL 使得普通的 Joe 开发者可以很容易地免费创建一个定制的 URL shortener，并将其部署到 GitHub🚀你甚至不需要拥有一个每月有 1700 万独立访客的网站😏

让我们开始吧。

步骤 0:转到 [jsonstore.io](https://www.jsonstore.io) 点击**紫色复制按钮**复制你的端点。

第一步:分叉/克隆 [mnml url repo](https://github.com/liyasthomas/mnmlurl) 在 GitHub 和可选的 [⭐开始项目](https://github.com/liyasthomas/mnmlurl) -显示一些❤的爱！

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[【liyassimas】](https://github.com/liyasthomas)/[【mnmlurl】](https://github.com/liyasthomas/mnmlurl)

### 🔗极小的 URL -支持自定义别名的现代 URL 缩短器&甚至可以在 GitHub 页面中托管[已弃用]

<article class="markdown-body entry-content container-lg" itemprop="text">

```
When I wrote this, only God and I understood what I was doing. Now, only God knows 
```

[![Liyas Thomas](img/073d9b1bed05dd7222f1228cb5ffbd21.png)](https://liyas-thomas.firebaseapp.com)

# 利亚斯·托马斯

<sub>Built with ❤︎ by [liyasthomas](https://github.com/liyasthomas) and [contributors](https://github.com/liyasthomas/mnmlurl/graphs/contributors)</sub>

* * *

[![Build Status](img/7dde4a18d5c49bb84dea6be20863cf7e.png)](https://travis-ci.org/liyasthomas/mnmlurl)[![GitHub release](img/39d086bc20b2b71642534b64e14f14b1.png)](https://github.com/liyasthomas/mnmlurl/releases/latest)[![repo size](img/48bd8fa71b6ae2cff63dd44b5deb85dd.png)](https://github.com/liyasthomas/mnmlurl/archive/master.zip)[![license](img/737f29ef08d7da242e74fff954f47540.png)](https://github.com/liyasthomas/mnmlurl/blob/master/LICENSE)[![contributions welcome](img/415935da8031f15a94578a276d0ed929.png)](https://github.com/liyasthomas/mnmlurl/issues)[![Website](img/a413ecdb4cfcfb679ebb4bd536ec0f62.png)](https://liyas-thomas.firebaseapp.com)[![Donate](img/1bd17d8398624fb29a851dfcd4012ead.png)](https://www.paypal.me/liyascthomas)

# [![mnmlurl](img/2c22006a371f2c071a0fb3466a586600.png) ](https://github.com/liyasthomas/mnmlurlsrc/icons/icon-48x48.png) mnmlurl

### <g-emoji class="g-emoji" alias="link" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f517.png">🔗</g-emoji> Minimal URL 是一个现代的 URL 缩短器，支持由 [Liyas Thomas](https://github.com/liyasthomas) 开发的自定义别名

### 遵循本教程创建您自己的 URL shorter，支持自定义别名，甚至可以托管在 GitHub 页面:[如何在 10 分钟内免费创建您自己的自定义 URL shorter&部署到 GitHub <g-emoji class="g-emoji" alias="rocket" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f680.png">🚀</g-emoji>T3】](https://dev.to/liyasthomas/how-to-create-your-own-custom-url-shortener-in-10-minutes-for-free-deploy-to-github-2k6h)

[![mnmlurl](img/109735bdb6f842ed9fe5e52f2f9ba633.png)](https://github.com/liyasthomas/mnmlurlsrc/icons/screely.png) [![mnmlurl](img/3893f8cd79e9eb6c4ef5e66db55a218f.png)](https://github.com/liyasthomas/mnmlurlsrc/icons/screely2.png)

### 特色 <g-emoji class="g-emoji" alias="sparkles" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2728.png">✨</g-emoji>

<g-emoji class="g-emoji" alias="heart_eyes" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f60d.png">😍</g-emoji> **URL 简化变得简单** : mnmlurl 使用 [jsonstore.io](https://www.jsonstore.io) 通过 HTTPS 请求在端点 JSON 文件上存储数据。它适用于任何网址。

<g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji> **无服务器端登录**:您的隐私对我们很重要。我们从不记录、收集或提示向任何人出售您的数据。

<g-emoji class="g-emoji" alias="fire" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f525.png">🔥</g-emoji> **自定义和随机别名支持**:添加您自己的自定义别名或生成随机…

</article>

[View on GitHub](https://github.com/liyasthomas/mnmlurl)

第二步:**重要:**在`src/head.js`文件中找到`var endpoint`，并用你从`step 0`复制的自己的端点替换它。

```
var endpoint = "https://www.jsonstore.io/6b6xxxxxxxxxxxxxxxxxxxxxxxa98";
replace it with your endpoint 👆 
```

Enter fullscreen mode Exit fullscreen mode

第三步:进入你的回购设置> GitHub 页面。选择`master branch`作为部署源。

就是这样！网站将于`your_github_username.github.io/mnmlurl/src`托管

编辑 0:最小 URL 可以在任何网站托管服务上托管，如 GitHub/GitLab pages、Firebase/Heroku hosting、yoursite.com 等。对于托管在这样的自定义域中，它建议到`npm build`文件夹并上传`output`文件夹。*在 GitHub 页面上托管只是为了验证概念。*

编辑 1:如果你想进一步发展，可以考虑在 **[GitHub](https://github.com/liyasthomas/mnmlurl)** 上投稿。

编辑 2:重要提示:最小 URL 是一个实验项目。不要用它来缩短敏感内容/网址。jsonstore.io 端点总是公共的，使得每个人都可以看到缩短的 URL。

下面是 mnmlurl
[![UI](img/9dd941d955f5627c809b823a4a0beb33.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KHlNZEBR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/liyasthomas/mnmlurl/master/src/icons/screely.png) 的一些截图

[![UI](img/803bff54859bfbe5fc70062a7a636bad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OlWOLEoq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/liyasthomas/mnmlurl/master/src/icons/screely2.png)

额外收获:我还为 mnmlurl 创建了一个跨浏览器扩展✨。

### 随时投稿上 **[GitHub](https://github.com/liyasthomas/mnmlurl)**

* * *

如果你想知道我的下一个项目，或者只是想聊聊网络、生活和幸福，请在 Twitter 上关注我💙。如果我的任何项目帮助了你，请考虑[捐赠](https://paypal.me/liyascthomas)。

[![yep](img/08d245a0e0ac60e4eabc44ea116a63e9.png)](https://i.giphy.com/media/XbxZ41fWLeRECPsGIJ/giphy.gif)