# 渐进式 Web 应用程序:缓存策略

> 原文：<https://dev.to/mr_steelze/progressive-web-apps-caching-strategies-mf2>

在过去的几周里，我一直在温习渐进式网络应用程序，并对一些概念有了更好的理解，尤其是缓存策略。所以这是我写下我所学到的东西。

页（page 的缩写）斯:我不是一个好作家:)而且这还没有涵盖 PWAs 的基础知识

## 什么是渐进式网络应用

渐进式 web 应用程序(PWAs)是普通的 Web 应用程序，对用户来说就像是本地移动应用程序。

要了解更多关于 PWAs [的信息，请查看本页](https://developers.google.com/web/ilt/pwa/)

## 缓存策略

构建 PWAs 您将进行一些缓存。它可以是资产(css，js，图标，图像)，响应，甚至是一个离线页面。因此，选择最适合您的应用的策略非常重要。常见的缓存策略有:

### *仅缓存

仅缓存策略从缓存中返回一个资源，而无需访问网络。如果它不存在于缓存中，它就会失败，什么也不会发生，因为我们根本没有试图通过网络获取该资源。这种策略对于在安装服务工作者期间提供预先缓存的资产非常有用。

```
self.addEventListener('fetch', event => {
 event.respondWith(
   caches.match(event.request)
 );
}); 
```

Enter fullscreen mode Exit fullscreen mode

### *仅限网络

仅网络策略进入网络以获得资源。它从不存储在缓存中，也不在缓存中查找。如果陈旧或缓存版本不可接受(对于必须始终保持最新的数据)，这种策略非常有用。

```
self.addEventListener('fetch', event => {
 event.respondWith(
   fetch(event.request)
 );
}); 
```

Enter fullscreen mode Exit fullscreen mode

### *先缓存

缓存优先策略试图首先从缓存中获取资源。如果没有找到，我们就进入网络并缓存响应以备后续请求。如果在缓存中找到了资源，我们就返回它，并且不通过网络进行请求。这种策略对于动态缓存资产非常有用，对同一资产的重复请求会立即从缓存中返回。

```
self.addEventListener('fetch', event => {
 event.respondWith(
   caches.match(event.request).then(response => {
     if(response) return response
     fetch(event.request).then(response => {
       cache.put(event.request, response.clone());
       return response;
      });
    });
 );
}); 
```

Enter fullscreen mode Exit fullscreen mode

### *网络第一

网络优先策略总是试图首先通过网络获得资源。如果请求失败，我们就去缓存检查资源。如果请求成功，我们缓存并返回响应。这种策略对于总是需要新鲜的资源很有用。

```
self.addEventListener('fetch', event => {
 event.respondWith(
   fetch(event.request).then(response => {
     cache.put(event.request, response.clone());
     return response;
   }).catch(_ => {
     return caches.match(event.request);
   });
 );
}); 
```

Enter fullscreen mode Exit fullscreen mode

### *重新验证时失效

stale while 重新验证总是为缓存中的资源提供服务，然后通过网络发出相应的请求并进行缓存。

```
self.addEventListener('fetch', event => {
 event.respondWith(
   caches.match(event.request).then(response => {
     fetch(event.request).then(res= => {
       cache.put(event.request, res.clone());
      });
      return response;
   });
 );
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 那么接下来呢？

我们找到了。以上是你会发现自己经常使用的常用策略。阅读更多关于缓存策略的信息[阅读更多...](https://developers.google.com/web/ilt/pwa/caching-files-with-service-worker)

下面是我建立的两个基本的 PWAs，非常感谢反馈，喜欢，转发或明星。此外，将爱对这写的反馈。谢谢:)

1.  A basic weather forecast app [forecast sky](https://predictsky.com)

    > ![Fake Ilaje boy😒 profile image](img/adc662a534af8e6c68b8959b2f54a648.png)fake Iradj kid[@ Mr _ steelze](https://dev.to/mr_steelze)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)Another simple progressive Web App 【T17] A simple weather forecast app [predictsky.com](https://t.co/bdnLzuPSJp) T17 is built with common javascript, and the weather data comes from [T20】 @ darkskaapp 【T21] and is hosted on [@ netlife](https://twitter.com/Netlify) .
    > I would appreciate the feedback on this, Forward and start this project on [@ github](https://twitter.com/github)
    > repurchase: [github.com/Steelze/ WEA …](https://t.co/R7Fc8dgf7O)
    > July 06, 2019 at 10: 09 AM[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png) ](https://twitter.com/intent/tweet?in_reply_to=1147447525630205952) [ ![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png) ](https://twitter.com/intent/retweet?tweet_id=1147447525630205952) [ ![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png) [T43](https://twitter.com/intent/like?tweet_id=1147447525630205952)

2.  货币转换器[货币转换器](https://steelze.github.io/currency-converter/)

    > ![Fake Ilaje boy😒 profile image](img/adc662a534af8e6c68b8959b2f54a648.png)假伊拉杰小子😒[@ Mr _ steelze](https://dev.to/mr_steelze)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)大家早上好。所以我决定重做 ALC 3.0 渐进式网络应用程序项目,我会感谢反馈.
    > 
    > 项目网址:[steelze.github.io/currency-conve…](https://t.co/tnoPwGPPei)
    > GithubRepo:[github.com/Steelze/curren…](https://t.co/G3Qg23NifS)2019 年 6 月 22 日上午 06:42[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1142321922853785603)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1142321922853785603)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1142321922853785603)