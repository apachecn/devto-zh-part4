# 使用预取和缓存实现更好的 JavaScript 包加载

> 原文：<https://dev.to/antjanus/using-prefetch-and-caching-for-better-javascript-bundle-loading-2p56>

这个想法在我脑子里酝酿了一段时间。预取是*所以*是有用的，但是我看到人们只用它来获取下一页。

但是，如果您用它来预取应用程序的新版本会怎么样呢？大家说说吧。

## 用例

用例是我工作中的用例。我还没有完成这个，但我正在认真考虑这个问题，可能会发出一个拉取请求，让团队的其他人看到。

用例是这样的:我们有一个沉重的前端。但是一旦它被加载，你就要打开那个标签。我不在脸书工作，那里的期望是一天打开和关闭那个标签 200 次。我工作的地方，人们日复一日地使用我们的应用来完成他们的工作。

为了解决前端繁重的问题，我们大量缓存。我们最初的无缓存加载时间是 2.6 秒(根据 Firefox，但第一次绘制要快得多)。缓存负载大约是 1 秒。对电子商务来说是可怕的，对一个像桌面应用一样使用的应用来说是极好的。

> 不要用本地的表演来说服我。Outlook 需要 10-15 秒才能加载到我的重量级机器上。Photoshop 需要一分钟。

我想不出有哪一款原生桌面应用的加载时间低于 500 毫秒。（...排除 Linux 和 Linux 终端上任何疯狂的轻量级东西，这种比较是不公平的:)。

## 散列法

如果您正在缓存，预取特别有效！所以一定要做到这一点。担心缓存破坏技术？使用 [Webpack 的缓存指南](https://webpack.js.org/guides/caching/)。本质上，Webpack 将把你的应用程序和一个确定的 hashname 捆绑在一起(我假设是这样的)。这样，如果您部署到生产环境中，您的新更改将导致 hashname 发生变化，并且在再次加载之前不会被缓存。

最棒的是，如果您将所有的供应商文件加载到一个单独的包中，这样，这个包可以保持缓存(大概)直到您的依赖性需求发生变化。你猜怎么着？那也在指南里。

> Webpack 的缓存指南让这篇文章中所有疯狂的酷东西得以实现。

## 缓存

然后，不管你用的是什么后端，只要在静态文件上设置缓存过期头就行了。如果您使用 Express with Node，以及 Express 的静态处理程序，您可以这样做:

```
app.use(express.static('./path/to/static/files/probably/build/folder', {
  setHeaders: (res) => {
     res.setHeader('Cache-Control', 'max-age=640800, public');
     res.setHeader('Pragma', '');
  })
}); 
```

Enter fullscreen mode Exit fullscreen mode

我建议阅读更多关于您的特定框架/语言的内容，以便更好地理解如何正确设置这些头。您不希望意外缓存不应该缓存的响应或资产

## 预取

从本质上讲，预取就是在需要之前从服务器获取数据。这可以是任何种类的数据；然而，在我们的例子中，我讨论的是预取 JavaScript 包。

在我的例子中，我提倡预取当前应用程序的一个更新版本，这样下次用户刷新时，他们就能看到你的改变，但他们不必等待额外的时间。

你有没有注意到有新版本发布时会通知你的应用程序？不假思索，我能想到两个:Todoist 和 YNAB(你需要一个预算)。两者都会在有变化时通知我，并提示我刷新。我还记得检查他们是否预取了新版本的 JS 包，但是如果他们没有，他们就错过了这个机会。

本质上，我主张无缝升级到沉重的前端包。

## 至此

那么我们目前有什么发现？

1.  在文件名中包含确定性散列的 JavaScript 包，用于标识新包是否可用
2.  独立的供应商捆绑包，其更新频率甚至低于主捆绑包
3.  JavaScript 捆绑包的主动后端缓存。我相信最大年龄被设置为大约 7 天。你可以把它变长。

因此，两次部署之间的任何页面刷新和页面加载都会被大量缓存，用户的浏览器不会向服务器请求这些 JavaScript 包。

在我的例子中，仅这一点就导致我的页面加载跳过两个 400 毫秒的网络请求(这是在快速网络上)，并在大约 70-80 毫秒时从缓存中获取它们。

## 轮询新捆绑包版本

事情是这样的。我解释了什么是预取，但是实际上如何让它工作呢？

我们需要做几件事，其中之一是轮询服务器的变化。这可以用不同的方法来完成。这么说吧，我们会时不时地访问一个 API 端点来检查变化。这非常简单，但是让我们使用`setInterval`

```
let currentVersion = process.env.BUNDLE_VERSION;
const interval = 1000 * 60 * 15; // 15 minutes

setInterval(() => {
  fetch('/api/version')
   .then(res => res.text())
   .then(version => {
     if (version !== currentVersion) {
       prefetchNewBundle(version);
       currentVersion = version; // to prevent further prefetching
     }
   });
}, interval); 
```

Enter fullscreen mode Exit fullscreen mode

不错！注意`currentVersion`被设置为`process.env.BUNDLE_VERSION`。开箱即用是行不通的。使用类似于 [Webpack EnvironmentPlugin](https://webpack.js.org/plugins/environment-plugin/) 的东西来嵌入版本。或者，您可以编写一些逻辑来找到您的脚本标签，并找出文件的散列。

例如(...而且这是一个肮脏的例子):

```
const scriptTag = document.querySelector('script'); // given you only have one script tag
const srcArr = scriptTag.src.split('/');
let currentVersion = srcArr[srcArr.length - 1].replace('.js', ''); 
```

Enter fullscreen mode Exit fullscreen mode

这应该会产生类似于`app.hash1234565`的结果。这已经足够好了。

在后端(例如在 Express 中)，我们可以添加端点来返回相同的`app.hash`签名:

```
app.get('/api/version', (req, res) => {
  // some mechanism to get the bundle name
  res.send(appVersion);
}); 
```

Enter fullscreen mode Exit fullscreen mode

甚至不需要被认证

## 如何预取

预取有几种方式，预加载内容有几种不同的机制。有很多关于这个主题的资源。我认为[艾迪·奥斯马尼](https://addyosmani.com/blog/prefetching/)的任何东西都是最好的资源，所以让我们采用他的解决方案。

根据前面的间隔示例，让我们定义一下`prefetchNewBundle`可能是什么样子。本质上，我们希望在我们的`<head>` :
中有一个预取链接标记

```
<link rel="prefetch" href="/app.hash123.js" /> 
```

Enter fullscreen mode Exit fullscreen mode

这应该可以了。鉴于此，你可以把`prefetchNewBundle`写成:

```
function prefetchNewBundle(newVersion) {
  const linkTag = document.createElement('link');
  linkTag.href = `/${newVersion}.js`;
  linkTag.rel = 'prefetch';
  linkTag.as = 'script';

  document.head.appendChild(linkTag);
} 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。那就行了！如果您将`rel`设置为`prefetch`，浏览器将获取该 JavaScript 包并缓存它。rel 有几个选项来决定加载优先级，但我不想一一列举。在我们的例子中，`prefetch`符合用例:预取作为对服务器的低优先级请求发生。这意味着它不会打断页面上正在进行的任何活动。

**注意:**使用`link`标签可以确保无论你获取什么文件，它们都不会被执行。:)

## 通知用户

虽然我们做了`prefetch`我们的资源，我们从来没有让用户知道！在`prefetchNewBundle`函数中，我们可以很容易地提示用户刷新、显示通知或其他任何有意义的事情。用户将重新加载页面，但是请等待！JavaScript 将已经存在并准备好使用:)

```
 function prefetchNewBundle(newVersion) {
        const linkTag = document.createElement('link');
        linkTag.href = `/${newVersion}.js`;
        linkTag.rel = 'prefetch';
        linkTag.as = 'script';
        document.head.appendChild(linkTag);

+        alert('New version of the application is available! Please refresh to enjoy all the hard work we put into our releases!');
    } 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**你*可以*使用文档提醒来指出新特性，但是显示通知/标题栏/其他东西来告诉用户新版本可能更有意义

## 概念证明

这是一个概念证明。确保等待 10 秒钟以查看预取。我手动检查了我的浏览器的缓存，以验证它被预取，它是！你也可以这样做。预取不会显示在你的网络标签中，所以你需要手动检查你的浏览器的缓存(在 Firefox 上，复制/粘贴这个 URL: `about:cache?storage=disk&context=`)

[https://codesandbox.io/embed/prefetch-poc-9ssi6](https://codesandbox.io/embed/prefetch-poc-9ssi6)