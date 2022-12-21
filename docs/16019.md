# 解释了服务工作者和缓存策略

> 原文：<https://dev.to/paco_ita/service-workers-and-caching-strategies-explained-step-3-m4f>

这一集将涵盖 PWAs 的其他重要主题:**服务人员** (SW)和**缓存策略**，我们可以实施这些策略来进一步发挥 PWAs 的全部潜力。

[![excited](img/827f15ccdc47f9b07910f9a9b8db8dbf.png)](https://i.giphy.com/media/yoJC2GnSClbPOkV0eA/giphy.gif)

激动吗？开始吧！

## 什么是服务人员？

SW 类似于 web worker，都是简单的 javascript 文件。web worker 没有特定的任务，它通常用于卸载主线程(主 web 应用程序正在其上运行)。

另一方面，服务工作者有一个特定的任务，它充当我们的 web 应用程序和网络之间的代理。它可以拦截 http 请求，并根据我们实现的缓存策略(稍后将详细介绍)，从网络或本地缓存提供响应。

让我们列出一些软件特征:

### 安全

服务人员将**仅在 HTTPs 连接**上运行。

这是一个安全的决定，因为否则我们很容易将我们的应用程序暴露给`man-in-the-middle`攻击。让我们想象一下，如果有人可以用一个被操纵的 SW 来代替我们的 SW，会发生什么...很可怕，不是吗？

[![scared](img/106820190c24b86feda71bfbd22f2ee9.png)](https://i.giphy.com/media/bEVKYB487Lqxy/giphy.gif)

另一方面，localhost 被认为是安全的，允许在部署应用程序之前对其进行测试。
如果我们使用 Angular，我们不能使用`ng serve`命令在本地构建和服务我们的应用程序，因为它不支持服务人员。在这种情况下，我们必须使用自己选择的 HTTP 服务器，例如 [http-server](https://www.npmjs.com/package/http-server) 包或 [Web 服务器](https://chrome.google.com/webstore/detail/web-server-for-chrome/ofhbbkphhbklhfoeikjpcbhemlocgigb?hl=en) Chrome 扩展。

### 没有直接 DOM 交互

服务人员不能直接访问 DOM。然而，它们可以通过`postMessage`接口与它们范围内的页面进行通信。然后，这些页面可以操纵 DOM，从而允许间接访问。

### 无阻塞

SW 运行在一个独立的线程上，独立于我们的应用程序所使用的线程。由此定义“无阻塞”。的确，我们不希望高负载操作或崩溃以任何方式影响我们 web 应用程序的性能。

在后台运行的能力也是我们可以向用户显示推送通知的原因，即使他们没有主动访问我们的网站。

## 生命周期

```
if ('serviceWorker' in navigator) {
    navigator.serviceWorker.register('/serviceWorker.js')
      .then(function(registration) { 
         // The registration was successful 
      })
      .catch(function(err) {
         // The registration failed
      });
  } 
```

Enter fullscreen mode Exit fullscreen mode

正如我们之前看到的，并不是所有的浏览器都支持软件。因此，当用户访问我们的应用程序并加载页面时，在尝试注册软件之前，我们首先需要检查服务工作者 API 是否可用。

[![sw-lifecycle](img/98961f00a284c43c57f837314c093d6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aWxZgF59--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8yii6mhho9qjm9vuiwza.png)

上面的模式描述了服务人员的不同生命周期步骤。
在注册过程中，如果出现错误或无法获取软件文件，整个操作将被取消。
当用户再次加载页面时，`register`方法将被重新触发。浏览器能够识别软件是否已经安装，并相应地调用方法。

一旦注册，一个软件不会一直保持激活状态。当事件需要被触发时，浏览器可以不可预测地终止它并重新激活它。这就是为什么，如果我们需要持久化服务工作者内部使用的状态(我这里不是指缓存资产或 API 请求)，我们应该更好地使用 IndexeDB 或类似的解决方案。

在`install`步骤中，通常执行完美操作。他们的目标是确保下载目标资产，并使其在软件的缓存中可用。这些资产通常是静态文件(如 js、css ),代表我们的应用程序的核心外壳，最小的文件和样式，即使在离线时也应该立即对用户可用。

⚠️，我们必须意识到，在这个阶段不要隐藏太多的资产。事实上，如果发生错误或软件无法缓存所有指定的资源，那么整个安装阶段将被中止，软件将不会在客户端被激活。一旦用户新访问或重新加载网页，安装步骤将被再次触发。

该步骤仅在软件生命周期开始时或服务器上有新版本时发生。

```
var urlsToCache = [
  '/',
  '/styles/styles.css',
  '/script/home.js'
];

self.addEventListener('install', function(event) {
  event.waitUntil(
    caches.open('my-cache')
      .then(function(cache) {
        return cache.addAll(urlsToCache);
      })
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

安装结束后，软件被激活。然而，软件不会立即控制我们注册的页面。这是为了与页面内容保持一致。根据我们的实现，一旦我们第二次访问/重新加载页面，我们的服务人员将完全控制它，并最终获取新的资产。

在`activate`步骤中，我们通常会清除旧的缓存。我们不能在安装步骤中这样做，否则仍然使用旧缓存的现有服务工作人员将会出现不可预测的行为，并可能导致错误。

下面的代码片段显示了我们如何删除所有未列入白名单的缓存。

```
self.addEventListener('activate', event => {

var validCaches = ['home-cache-v2', 'articles-cache-v2'];

  event.waitUntil(
    caches.keys().then(keys => 
    Promise.all(keys.map(key => {
        if (validCaches.indexOf(key) === -1) {
          return caches.delete(key);
        }
      })
    )).then(() => {
      // We successfully deleted all the obsolete caches
    })
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

此时，如果您打开 DevTools，访问`Application`选项卡并单击`service worker`部分，您会发现在我们的浏览器中注册了开发软件(dev.io ):

[![sw-tab](img/620e33736948b6d1d2e3e5e7969a2217.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EIeYDQJU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vw3ehjbqw6vx118jq81p.png)

### 更新服务人员

如果一个新的服务工作者版本可用(一个简单的字节差异使它成为一个新版本)，它将在用户访问我们的 web 应用程序时被下载和安装。然而，新的服务人员不会立即替换旧的服务人员，而是保持在安装步骤中，*等待*被激活。

浏览器确保客户端上只有一个活动的服务工作器版本。只有当运行 PWA 的所有选项卡都关闭，或者用户导航到不同的 URL，然后返回到我们的 PWA 时，新的服务人员才最终被激活。知道这一点很好，因为简单地刷新页面是不够的，而且经常会带来混乱。

### 注销服务人员

要删除软件，您可以点击浏览器开发工具中的`Unregister`链接。

*   **Chrome:** 点击 F12 >应用标签>服务人员部分

[![sw-tab](img/620e33736948b6d1d2e3e5e7969a2217.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EIeYDQJU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vw3ehjbqw6vx118jq81p.png)

*   **火狐:**地址栏中的数字`about:debugging#workers`:

[![firefox](img/e0b4d01bbd8497e44dd079e224aac390.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qYi7nfEc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kgmj7r8x9sztooauvvl0.png)

或者也可以通过编程:

```
navigator.serviceWorker.getRegistration()
   .then(function(registration) {
       if(registration){
          registration.unregister()
          .then(
               function(success) {
                 // if success = true, unregister was successful
                });
          }
    }); 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**注销软件不会释放其缓存。为此，我们必须在开发工具(Chrome)的应用程序面板中单击“清除存储”按钮:

[![clear-cache](img/30e5e6b8bf854587aa7fadc86da23bf4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0vVKmYUL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2seeb7ec29dutocgrx8q.png)

通过代码，我们可以使用`caches.delete()` :

```
if ('caches' in window) {
    caches.keys()
      .then(function(keyList) {
          return Promise.all(keyList.map(function(key) {
              return caches.delete(key);
          }));
      })
} 
```

Enter fullscreen mode Exit fullscreen mode

`caches.keys()`方法返回`CacheStorage`的键，这个接口代表服务工作者可以访问的`Cache`对象的存储。

## 缓存策略

我们可以采用不同的缓存策略来提高项目的性能。事实上，如果一个数据请求被缓存，我们完全不需要访问网络就可以交付它。这带来了以下优势:一方面，响应快得多，另一方面，即使客户端离线，我们也可以提供数据，因为我们已经在本地获得了数据。

不过，在开始之前，我们需要了解我们需要指导软件如何缓存数据请求，因为默认情况下不会这样做。

### 通用缓存策略

下面是一些常见的缓存策略，不绑定任何框架。

#### 仅缓存

[![cache-only](img/ed0a278c2824c755318e3dab1606dba8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yIlYPONk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nchwiq59u3h0go7qfamm.png)

来源:谷歌网络基础指南

```
self.addEventListener('fetch', function(event) {

  event.respondWith(caches.match(event.request));
  // If the requested data isn't in the cache, the response
  // will look like a connection error
}); 
```

Enter fullscreen mode Exit fullscreen mode

可能是最简单的一个。软件期望在这里找到所请求的资产。这种策略可以用于构成我们的“应用程序外壳”的静态资源。通常在软件安装时获取，以便在此阶段后在缓存中可用。

#### 仅网络

[![network-only](img/114dd71f7ab52e95c20461d12bb94fd5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R9RdwYJe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5j375yeuto7kc8vz60s4.png)T3】

```
self.addEventListener('fetch', function(event) {
   // We can put some custom logic here, otherwise
   // the request will follow the default browser behaviour
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个策略与前一个正好相反:我们总是访问网络，甚至不查询缓存。这最适合于日志或任何我们不需要的东西，让它可以脱机使用。

#### 重新验证时失效

[![stale-revalidate](img/bf815f50725f80fd6221a575c0f4e9e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EYWf7DAm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8ia2qwmw9jo3lyb86r80.png)T3】

```
self.addEventListener('fetch', function(event) {

    event.respondWith(async function() {
        const cache = await caches.open('cache-v1');
        const cachedResponse = await cache.match(event.request);
        const fetchPromise = fetch(event.request);

        event.waitUntil(async function () {
            const networkResponse = await fetchPromise;
            // Update the cache with a newer version
            await cache.put(request, networkResponse.clone());
        }());

        // The response contains cached data, if available
        return cachedResponse || networkResponse;
    }());
}); 
```

Enter fullscreen mode Exit fullscreen mode

与仅缓存策略类似，目标是通过从缓存中传送数据来确保快速响应。然而，当客户端请求被处理时，一个单独的请求被触发到服务器以获取一个更新的版本，如果可用的话，并将其存储到缓存中。这样，当我们在一方面保证快速数据交付的同时，我们也在另一方面更新缓存的数据，所以下一个请求将接收到更真实的版本。

### 角度缓存策略

Angular 只提供两种缓存策略:

**性能**(默认)
[![Performance-diagram](img/744687828619cf1284def7a94ef37ad1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IZWH5Wt_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aghjzas3ipm724uccuqs.png) 
这里的目标是优化响应时间。如果缓存中有可用的资源，则传递该版本。否则，执行网络请求以获取并缓存它。
这种策略适用于不经常改变的资源，如用户简档图像。在这些情况下，我们希望为用户提供最快的响应，而不用担心交付可能过时的数据。

**新鲜度**
[![Freshness-diagram](img/e3d66d3dec023550297e3fd847f60e27.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jZvEvbW_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5aafkz9yt35dwisuaekz.png) 
当需要从网络传递最新数据时。我们可以指定一个超时时间，在此之后，请求会退回到缓存中，并尝试从缓存中传递所需的数据。这种策略的一个典型应用是当我们需要交付经常变化的最新信息时。我们可以想到一个处理股票价格或博客文章的应用程序。

对于角度策略，我不会说得太详细，因为我们将在下一篇文章中看到它们的更多细节，我们也将编写一些代码来实现它们。
T2】

## 如何摆脱“僵尸”服务人员？

如果我们不再与服务人员合作，并希望摆脱客户中所有旧的、已安装的服务人员，我们可以使用以下方法:

1 -删除我们在新应用中注册软件的代码(因此不会注册新软件)

2 -用以下代码替换(旧的)软件文件内容:

```
caches.keys()
    .then(keys =>
        Promise.all(keys.map(async key => await caches.delete(key)))
    .then(async () => await registration.unregister())
    .catch((err) => console.error('Something went wrong: ', err)); 
```

Enter fullscreen mode Exit fullscreen mode

这将产生两个影响:

1-当新客户加载我们的 PWA 时，不会安装新软件

2 -已安装软件的客户端将在再次加载应用程序后下载新软件。否则，浏览器会自动检查(自上次检查起最多 24 小时后)是否有新的软件版本，并用新的软件代码替换旧的软件代码。

在这两种情况下，新代码将删除缓存并卸载当前软件。

## 我们可以存储多少数据？

在任何情况下，可用的数量都不相同，但根据设备和存储条件的不同，每个浏览器的可用数量也会有所不同。

[![cache-quota](img/2225733ad1c85b45c9c4c92d7003e84e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HXTjk2fC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kj5a5b9pdfkp708vrj26.png)

Chrome 和 Opera 基于每个源(我们的站点域)的可用存储。要验证剩余空间，我们可以使用 [`Quota Management API`](https://developer.mozilla.org/en-US/docs/Web/API/StorageQuota) :

```
navigator.storageQuota.queryInfo("temporary")
  .then(function(info) {
     console.log(info.quota);
     // It gives us the quota in bytes

     console.log(info.usage);
     // It gives us the used data in bytes
}); 
```

Enter fullscreen mode Exit fullscreen mode

当达到特定阈值时，一些浏览器开始提示用户是否同意继续存储更多数据。

*   存储 50MB 数据后的 Firefox
*   Safari mobile 最多只能使用 50MB
*   Safari desktop 没有储存空间限制(😳)，但在存储 5MB 后开始请求确认。

这些最初的职位侧重于公共艾滋病的理论基础。下一篇文章将向我们展示创建 pwa 工具。
此外，我们将与 Angular 创建一个演示，我会一步一步地指导你，使之成为一个完整的渐进式网络应用程序。然后你可以把它作为你下一个项目的起点！

你可以关注我:

[![follow-me-twitter](img/ff8782766b4760eb9020cec54eb02af8.png)](https://twitter.com/paco_ita)