# 如何构建 PWA

> 原文：<https://dev.to/wes_goulet/how-to-build-a-pwa-2kgd>

*这是来自[我的博客](https://goulet.dev/posts/how-to-make-a-pwa/)的交叉帖子。*

## 什么是渐进式 Web App (PWA)？

我试图保持这篇文章的简单，所以我将用我自己的简单术语来定义什么是 PWA。PWA 是一款网络应用，它使用一系列浏览器/操作系统增强功能，使其看起来更像一款原生应用。

> 如果你想要一个更官方的定义，请看维基百科或 T2 谷歌网站。

## PWA 基础知识

如何将一个网站/web app 提升为一个进步的 web app？[这里有一份不错的 PWA 清单](https://developers.google.com/web/progressive-web-apps/checklist)...但这是一个很长的列表。我们从最基本的开始，简单点，好吗？

对我来说，PWA 至少有以下 4 个特征:

### 1。安全(服务于 HTTPS)

我曾经认为在 HTTPS 提供内容是一件困难而昂贵的事情...然后[让我们加密](https://letsencrypt.org/)出现了，让它免费...然后 [Netlify](https://netlify.com) 出现了，让它变得疯狂简单(而且仍然免费)。事实上，多亏了 Netlify，在带有 HTTPS 自定义域的 CDN 上托管静态站点就像推送 git commit 一样简单。顺便说一句，这不是广告。我只是喜欢工作良好的简单工具。

因此，注册 Netlify 并连接你的 github/gitlab/bitbucket repo，你将很快在 HTTPS 提供你的网站/应用。关于更多细节，[这里是 netlify 自己的部署指南](https://www.netlify.com/blog/2016/09/29/a-step-by-step-guide-deploying-on-netlify/)。

如果你已经有了主机设置，那么可能会有一些关于设置 SSL/HTTPS 的教程，可以在网上快速搜索一下。请确保您帮了用户一个忙，将所有 HTTP 流量重定向到 HTTPS(顺便说一句，Netlify 为您做到了这一点),这样用户就不会看到这一点:

[![Not Secure Connection](img/27f7dfac849d34d2480fe29e2706fd42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Snc0_V2n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t9oi58vww0knhqxv2pfa.png)

### 2。全屏/独立

我不认为每个 web 应用程序都应该固定在主屏幕上，像一个独立的应用程序一样运行——一些 web 应用程序很少使用，在浏览器中作为收藏夹/书签也很好。但如果用户希望更快地访问您的 web 应用程序，并将其固定在他们的主屏幕/桌面上，那么让我们通过不显示浏览器“外壳”来提供良好的体验。

[![Browser vs Standalone](img/153ed884337169293ce3ebeea22f5278.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t1yl7SCF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m3ylztwteajqn66i6t4m.png)

要让你的 web 应用在浏览器“外面”显示(如右图)，你只需要在你的页面上添加一个 [web 应用清单](https://developer.mozilla.org/en-US/docs/Web/Manifest)，并将`display`设置为`standalone`。清单示例如下:

```
{  "name":  "My App",  "scope":  "/",  "display":  "standalone",  "start_url":  "./index.html",  "theme_color":  "#b859ed",  "background_color":  "#000000",  "icons":  [  {  "src":  "./icons/icon-72x72.png",  "sizes":  "72x72",  "type":  "image/png"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

然后在你的 HTML 中确保链接到你的清单:

```
<html>
    <head>
        <!-- other stuff in head -->
        <link rel="manifest" href="./manifest.json" />
    </head>
    <body>
        <!-- body stuff -->
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

Android 和 iOS 都尊重您的清单中的显示属性，因此保持简单。不幸的是，iOS 并不尊重清单中的所有内容，这也给我们带来了麻烦...

## 3。启动图标

一个好的 PWA 体验包括当你的应用程序被固定在主屏幕上时有一个真正的图标。

[![No Icon vs Icon](img/a7ed4225993551c3b4f65deb5e03cec6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ofbrg0gn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0yb9mpsa0en1rls6ijwd.jpg)

提供图标的标准方式是在 manifest.json 中填写`icons`数组，就像上面的示例清单一样。不幸的是，iOS(甚至最新的 iOS13 公测版)并不尊重这个值，所以对于 iOS 你需要在你的 HTML 中添加“apple-touch-icon”链接:

```
<html>
    <head>
        <!-- other stuff in head -->
        <link rel="apple-touch-icon" sizes="180x180" href="./icons/logo180.png" />
    </head>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

### 4。服务行业人员

关于如何使用服务人员，可以写很多东西...但是现在让我们保持简单。让我们来设置一下，这样您的 web 应用程序每次都会从缓存中提供应用程序的“外壳”，并在后台检查任何更新(这样应用程序在下次启动时就是最新的)。

我总是担心我的应用程序过于依赖第三方库，但在这种情况下，我认为 [workbox](https://developers.google.com/web/tools/workbox/) 是一个很好的解决方案。

> 如果你想看到一个手写的服务人员，看看背景噪音的 [sw.js。](https://github.com/wes566/background-noise/commit/8307dfb762f5c676a683705bcceeedcf1387174c#diff-36a6321ccec60e74fcb19c8b57d29b8d)

首先将这个小脚本放到 HTML 中，注册您的服务人员(在支持它的浏览器上)。请注意我们是如何在页面加载后加载服务人员的，这样就不会降低初始页面加载的速度。

```
<html>
    <head>
        <script>
            if ('serviceWorker' in navigator) {
                window.addEventListener('load', () => {
                    navigator.serviceWorker.register('./sw.js');
                });
            }
        </script>
    </head>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

现在创建一个`sw.js`文件:

```
importScripts('https://storage.googleapis.com/workbox-cdn/releases/4.3.1/workbox-sw.js');

if (workbox) {
    workbox.routing.registerRoute(
        /\.(?:html|js|css|png|jpg|jpeg|svg|gif)$/,
        new workbox.strategies.StaleWhileRevalidate()
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

> 注意，这里的工具箱版本是硬编码的。最终你可能会想查看一下 workbox 网站的最新版本，看看它是否值得更新。

这个文件只是说所有的`html`、`js`、`css`和图像文件将从缓存中提供，然后在后台从网络更新。您可以在那里修改扩展 regex 来捕获其他文件类型(比如用于音频文件的`mp3`)。如果你的 web 应用程序从任何服务器端点获取这些格式的文件，要小心缓存`json` / `xml`文件，因为那样你的应用程序会显示陈旧的数据(即使在线)。缓存这样的数据超出了本文的范围，但也许我会在以后的文章中深入探讨。

## 示例 PWA

[背景噪音](https://backgroundnoise.app)是我的首选 PWA 示例。来源可以在 github 上找到[。我喜欢用这个作为例子，因为有时人们认为 pwa 必须作为 SPAs(单页应用程序)来构建，或者用 React 这样的前端框架来构建，但事实并非如此。任何网站/web 应用程序(我倾向于互换使用这两个术语)都可以增强为 PWA，甚至是普通的 HTML/CSS/JS web 应用程序。](https://github.com/wes566/background-noise)

> 希望这篇文章对你有帮助，如果你有任何问题，你可以在 Twitter 上找到我。