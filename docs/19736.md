# 从使用 Workbox 的服务人员的离线功能中吸取的经验教训

> 原文：<https://dev.to/samvloeberghs/lessons-learned-on-offline-capabilities-with-service-workers-using-workbox-143m>

*最初发布于[https://samvloeberghs . be](https://samvloeberghs.be/posts/lessons-learned-on-offline-capabilities-with-service-workers-using-workbox)2019 年 5 月 31 日*

## 目标受众

这篇文章的目标读者是正在创建能够脱机使用的应用程序的 web 开发人员，更具体地说，是能够脱机使用的应用程序，可以归类为 SPAs 或单页应用程序。我将分享我的一些经验教训，不仅仅是为了分享，也是为了**从感兴趣的读者那里得到反馈**，他们可能已经找到了其他方式或使用了其他方法来解决所描述的问题。

## 本帖我们会讨论什么？

1.  为所有深度链接提供`index.html`(离线时)
2.  不要一味的用`skipWaiting()`或者`clients.claim()`
3.  小心你在浏览器中缓存的内容
4.  避免在服务工作器中使用全局状态

这篇文章中使用的所有示例代码，就像这个完整的网站一样，可以在 Github 这里的找到[，作为一个示例项目供你测试和试用。](https://github.com/samvloeberghs/kwerri-oss)

## 为所有深度链接提供`index.html`(离线时)

在开发 SPA 应用程序时，我们经常利用路由的力量。**当我们离线时，我们需要确保所有路线保持工作**。一个例子:

1.  应用程序访问者的入口点是`/about`页面。服务器知道，对于你网站上的每一次点击，它都必须返回`index.html`文件。
2.  过一会儿，这个用户会回到你的网站，但是他会直接通过`/contact`页面刷新或者通过任何其他网站上的链接进入。
3.  由于某种原因，您的应用程序现在处于脱机状态，或者用户在导航过程中失去了连接。您的服务人员开始工作，试图在缓存中查找`/about`页面，但是没有找到，因为缓存的唯一静态文件是`index.html`文件。
4.  您的服务人员需要知道为这些类型的请求服务哪个文件

使用 Workbox，这可以通过检查请求是否为“导航”模式来轻松完成，如果是，则提供单个`index.html`文件。这个`index.html`文件应该已经作为静态资产缓存在 workbox 中，所以我们从`precache`请求它。

```
// sw.js

// default page handler for offline usage,
// where the browser does not how to handle deep links
// it's a SPA, so each path that is a navigation should default to index.html
workbox.routing.registerRoute(
  ({ event }) => event.request.mode === 'navigate',
  async () => {
    const defaultBase = '/index.html';
    return caches
      .match(workbox.precaching.getCacheKeyForURL(defaultBase))
      .then(response => {
          return response || fetch(defaultBase);
      })
      .catch(err => {
        return fetch(defaultBase);
      });
  }
); 
```

这种逻辑为您提供了**更快的离线和在线使用**，因为即使当您的用户在线时，服务人员也会使用缓存的`index.html`，例如，当您用应用程序的不同页面打开一个新标签时。

### 快捷键可用

Workbox 为这种行为提供了一种快捷方式和额外的选项，比如通过在`routing`模块上公开`registerNavigationRoute`处理程序来加入白名单和黑名单。

```
// sw.js

workbox.routing.registerNavigationRoute(
  // Assuming '/single-page-app.html' has been precached,
  // look up its corresponding cache key.
  workbox.precaching.getCacheKeyForURL('/single-page-app.html'), {
    whitelist: [
      new RegExp('/blog/')
    ],
    blacklist: [
      new RegExp('/blog/restricted/'),
    ]
  }
); 
```

*请记住，这反映了 SPA 设置情况下服务器的行为。**对于所有不匹配静态资源(如图像)或动态资源(如 API)的请求，服务器应该返回`index.html`文件**！*

## [T3 不要一味的用`skipWaiting()`或者`clients.claim()`](#dont-just-blindly-use-raw-skipwaiting-endraw-or-raw-clientsclaim-endraw-)

使用`skipWaiting()`或`clients.claim()`不应该盲目地、不加思考地进行。不幸的是，互联网上有许多文章只是指导你去做，而没有提供更多的信息。杰克·阿奇博尔德(Jake Archibald)的文章“[服务人员生命周期](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle)”中也明确提到了这一点。

***注**:我看到很多人包括`clients.claim()`都是样板，但我自己很少这样做。只有在第一次加载时才真正重要，而且由于逐步增强，页面通常在没有服务人员的情况下也能愉快地工作。*

### 可能会出什么差错？

在几种情况下，在你的服务人员**中盲目地呼叫`skipWaiting()`或`clients.claim()`可能会导致意想不到的有害副作用。**

一种可能的情况是，您的应用程序中的代码功能与您的服务人员中的逻辑不再匹配。所以这可能会带来突破性的变化。*让我们考虑一个简短的分步示例*:

1.  浏览器缓存了您的应用程序代码，包括注册您的服务工作者和服务工作者的代码
2.  加载应用程序时，会提供缓存的内容，并为您的新服务人员加载更新的功能，更新预先缓存的内容，最后跳过等待过程并声明客户端可用
3.  您的用户现在在浏览器中加载了旧的应用程序，但是使用了新的服务工作者功能，这可能会有问题！

因此，务必**确保应用程序逻辑的加载版本与服务人员逻辑的加载版本能够很好地协同工作**。

### 首次使用您的应用程序时处于活动状态的服务人员

不调用`skipWaiting()`或`clients.claim()`对您的用例来说可能是有问题的，特别是如果您希望您的服务人员在您第一次加载您的应用程序时就处于活动状态。让我们举一个基本的例子来解释这种情况:

1.  您的应用程序导入一个第三方库，该库向服务器发送请求。这些请求必须发送到的 URL 是可配置的，但是用于这些请求的头是不可配置的。您可能希望为每个请求添加一个`Authorization`头。
2.  可以使用的一种策略是设置一个服务工作者，充当代理，并将这个头添加到所有相关的请求中。
3.  这个功能必须从第一次开始就工作，但是你可能认为没有`skipWaiting()`和/或`clients.claim()`这是不可能的。

到目前为止，我发现的唯一方法是检查您的页面是否已经有一个激活的服务工作者，或者正在将其状态更改为激活，但没有新版本的服务工作者等待激活。在这种情况下，我们可以假设没有先前的服务工作者可用。现在，只要服务人员激活，我们就可以自动重新加载页面。

```
// service-worker-registration.ts

// Check that service workers are available
if ('serviceWorker' in navigator) {

    // Use the window load event to keep the page load performant
    window.addEventListener('load', () => {

    navigator.serviceWorker
      .register('/sw.js')
      .then(registration => {

        if (navigator.serviceWorker.controller) {
          // let the application know our service worker is ready
          window['serviceWorkerReady'] = true;
          window.dispatchEvent(new CustomEvent('service-worker-ready'));
        }

        // A wild service worker has appeared in reg.installing and maybe in waiting!
        const newWorker = registration.installing;
        const waitingWoker = registration.waiting;

        if (newWorker) {
          if (newWorker.state === 'activated' && !waitingWoker) {
            // reload to avoid skipWaiting and clients.claim()
            window.location.reload();
          }
          newWorker.addEventListener('statechange', (e) => {
            // newWorker.state has changed
            if (newWorker.state === 'activated' && !waitingWoker) {
              // reload to avoid skipWaiting and clients.claim()
              window.location.reload();
            }
          });
        }

      })
      .catch(err => {
        console.log('service worker could not be registered', err);
      });

  });

} 
```

缺点是第一次使用可能会出现闪烁。这种副作用可以通过延迟应用程序的引导来最小化，直到您知道服务工作器是活动的，并显示加载/闪屏。正如前面所解释的，这已经是一个需求了。

***有其他建议请告诉我！**我对其他方法很好奇，因为我对刷新不太满意..* *

## 小心你在浏览器中缓存的内容

当构建你的应用程序和考虑它的离线策略时，你一定要考虑你要离线缓存什么。为什么？因为不是所有的浏览器和设备在存储方面都有相同的功能。下表显示了每个浏览器和每个来源的可用配额。

| 浏览器 | 限制 |
| --- | --- |
| 铬 | 小于 6%的可用空间 |
| 火狐浏览器 | 小于 10%的可用空间 |
| 旅行队 | < 50MB |
| IE10 | < 250MB |
| 边缘 | [取决于体积大小](https://developer.microsoft.com/en-us/microsoft-edge/platform/documentation/dev-guide/storage/IndexedDB/) |

<small>这篇综述的所有荣誉归于文章作者[渐进式网络应用的离线存储](https://developers.google.com/web/fundamentals/instant-and-offline/web-storage/offline-for-pwa)<small></small></small>

 <small>对于在台式机或笔记本电脑的浏览器中运行的应用程序来说，这很可能总是足够的空间。但如果你在存储空间有限的平板电脑或手机上构建内容密集型应用，这可能很快就会成为一个问题..

想象一下，一台平板电脑有 32GB 的空间，但由于图片、音乐和拍摄的视频等原因，只剩下 3GB 的可用空间。这意味着，对于您的应用程序，该平板电脑上只有 6%的 3GB 可用空间，即大约 184 MB。

虽然 184 MB 似乎仍然是足够大的空间，但您必须考虑它可能不够大的可能性，特别是如果您正在缓存来自其他域(CORS)或不透明响应的大文件或资产。正如 T2·杰拉尔多·罗德里格斯的文章《当 7 KB 等于 7 MB 时》中所强调的，处理不透明的请求是很棘手的

一般来说，我们希望避免点击`DOMException: Quota exceeded`，因为这可能会破坏我们完整的服务工作者功能或离线能力。

Workbox 允许我们使用各种方法自动清理缓存。我们可以在一段时间后**使特定路由处理器的缓存无效，并且我们可以**在配额错误时**优雅地清除缓存。** 

```
// sw.js

// Cache the underlying font files with a cache-first strategy for 1 year.
workbox.routing.registerRoute(
  /^https:\/\/fonts\.gstatic\.com/,
  new strategies.CacheFirst({
    cacheName: 'google-fonts-webfonts',
    plugins: [
      new workbox.cacheableResponse.Plugin({
        statuses: [0, 200]
      }),
      new workbox.expiration.Plugin({
        maxAgeSeconds: 60 * 60 * 24 * 365,
        maxEntries: 30,
        purgeOnQuotaError: true // Automatically cleanup if quota is exceeded.
      })
    ]
  })
); 
```

## 避免在服务工作器中使用全局状态

我通过调试、调试、调试，直到我疯狂地开始搜索互联网，才艰难地学会了这一点。让我首先解释一下我的用例，它实际上是上面的`Authorization`头例子的起源。

1.  我们正在开发的应用程序包括像 [openlayers](https://openlayers.org/) 这样的第三方库，为我们提供了非常好的动态地图功能。
2.  该库用于加载地图切片的 URL 是可配置的，但标题是不可配置的。我们需要能够在对这些 URL 的请求上添加一个`Authorization`头，为此使用一个服务工作者似乎是合理的。
3.  我们一登录，就使用`BroadcastChannel` API 将令牌传递给服务人员，并将其存储在一个全局变量`let oAuthToken`中。
4.  每当我们的服务人员看到对地图切片的请求时，我们就代理这些请求，并将令牌作为请求的标题添加到请求中，这样我们的服务器/后端就可以成功地授权该请求。

### 现在出了什么问题？

似乎服务人员在他们的一生中可能会多次停止和重新启动，因为[一些优化，如电池节省算法](https://stackoverflow.com/questions/38835273/when-does-code-in-a-service-worker-outside-of-an-event-handler-run/38835274#38835274)。**停止并重启会破坏并重新初始化我们服务工作者的全局范围！**因此，我们保存令牌的全局变量消失了，导致请求没有正确授权。

### 那么如何正确的与服务人员分享`OAuthToken`？

从我们的应用程序逻辑与我们的服务工作者逻辑成功共享`OAuthToken`的唯一方法是使用 IndexedDB API。在我的服务工作者中，我用来简化访问的库是 [idb-keyval](https://github.com/jakearchibald/idb-keyval) 。如果你需要更高级的逻辑，你可以查看 [idb](https://github.com/jakearchibald/idb) 。

在下面的例子中，我们假设我们的应用程序在 IndexedDB 中保存了一个令牌。使用 workbox，我们注册一个`RegExp`用于我们想要使用令牌进行身份验证的任何路由。让我们一步一步地看这个例子:

1.  我们注册了一个路由，在这种情况下是为了捕获去往`map.png`的所有请求
2.  在路由注册的捕获功能(第一个参数)中，检查我们在 IndexedDB 中是否有可用的令牌。
3.  如果令牌可用，我们修改处理程序中的请求，即第二个参数，并添加令牌作为`Authorizatio` n 头。
4.  如果没有令牌可用，我们返回一个替换资源，即`not_authorized.png`图像，它已经在我们的预缓存设置中被缓存了。

```
// OAuth header interceptor
workbox.routing.registerRoute(
  ({ url }) => {
    return /map\.png/.test(url);
  },
  async ({ event, url }) => {

    // get the eventual token
    const customStore = new Store('swl-db', 'swl-db-store');
    const oAuthToken = await get('token', customStore);

    // if token available, set it as the Authorization header
    if (!!oAuthToken) {
      const modifiedHeaders = new Headers(event.request.headers);
      modifiedHeaders.set('Authorization', oAuthToken);
      const overwrite = {
        headers: modifiedHeaders
      };
      const modifiedRequest = new Request(url.toString(), overwrite);
      return fetch(modifiedRequest);
    }

    const defaultNotAuthedBase = '/assets/not_authorized.png';
    return caches
      .match(workbox.precaching.getCacheKeyForURL(defaultNotAuthedBase))
      .then(response => {
        return response || fetch(defaultNotAuthedBase);
      })
      .catch(err => {
        return fetch(defaultNotAuthedBase);
      });

  }
); 
```

*该功能的一个可能扩展是检查来自服务器的响应的状态码是否如预期的那样，例如一个`200 OK`。如果不是有效或预期的响应代码，则返回缓存的`not_authorized.png`图像。*

## 结论

当开发具有离线功能的 spa 时，我们需要确保我们的服务人员的行为方式与我们的服务器相同。对于路由请求或导航，服务人员应始终返回 index.html，以在离线时保持深度链接工作。

不要一味的用`skipWaiting()`或者`clients.claim()`，要想想它可能引入的副作用。相反，**让用户决定什么时候升级功能**或者让新版本自动应用到应用程序的后续加载中。

请注意您离线缓存的内容以及您的应用程序将在哪些设备上使用。**应用程序的可用空间取决于加载应用程序的设备和浏览器的可用空间**。

不要依赖于**服务工作者中的全局变量或状态，因为服务工作者在其生命周期中可能会被销毁和引导几次**。使用 IndexedDB 与服务人员共享应用程序的状态。

## 进一步阅读

*   [基于请求类型的服务工作者缓存策略](https://medium.com/dev-channel/service-worker-caching-strategies-based-on-request-types-57411dd7652c)
*   [工具箱文档](https://developers.google.com/web/tools/workbox/)
*   [服务人员生命周期](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle)
*   [服务人员提示](https://github.com/popeindustries/sw-tips)
*   [渐进式网络应用的离线存储](https://developers.google.com/web/fundamentals/instant-and-offline/web-storage/offline-for-pwa)
*   [当 7 KB 等于 7 MB 时](https://cloudfour.com/thinks/when-7-kb-equals-7-mb/)
*   事件处理程序之外的服务工作器中的代码何时运行？
*   [高性能服务人员加载](https://developers.google.com/web/fundamentals/primers/service-workers/high-performance-loading)

## 特别感谢

*   尤尔根·范·德·莫尔

感谢您审阅这篇文章并提供宝贵的反馈！我欢迎任何其他反馈，请让我知道！

*最初发布于[https://samvloeberghs . be](https://samvloeberghs.be/posts/lessons-learned-on-offline-capabilities-with-service-workers-using-workbox)2019 年 5 月 31 日*</small>