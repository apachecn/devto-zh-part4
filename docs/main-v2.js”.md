# PWA:渐进式 Web 应用程序的大量提示和技巧列表

> [https://dev . to/oieduardorabelo/pwa-uma-大型逐步 web 应用程序-4kcc-t1】提示列表](https://dev.to/oieduardorabelo/pwa-uma-grande-lista-de-dicas-e-truques-para-progressive-web-apps-4kcc)

我最近做了很多关于网络应用程序的演讲。演讲结束时，通常会有问答环节供观众提问或提出想法。在这些会议中，我经常被问及一些真正有用的问题，这些问题值得与更广泛的受众分享。

在这篇文章中，我列出了一些经常向我提出的关于渐进式 Web 应用程序和服务工作人员的问题，并试图尽可能多地包含准确的答案。其中有些问题可能看起来很明显，有些则不那么明显——但总的来说，我希望它们有用！

所以呢-我...。我们走！这份实用提示、技巧和技巧的清单，可以帮助您构建下一个渐进式的 Web 应用程序，完全没有特别的顺序！

* * *

# 如何知道有多少用户正在使用我的站点上的“添加到闪屏”(A2HS)功能？

当 2hs(*add to homecreen*横幅出现时，您可以侦听`beforeinstallprompt`事件，以确定使用者在呈现横幅时所做的选择。

下面的代码在操作中显示了这一点:

```
window.addEventListener("beforeinstallprompt", function(event) {
  event.userChoice.then(function(result) {
    if (result.outcome == "dismissed") {
      // Usuário dispensou o banner, enviar para o nosso analytics
    } else {
      // User accepted! Send to analytics
      // Usuário aceitou o banner, enviar para o nosso analytics
    }
  });
}); 
```

使用上面的代码，您可以确定用户是丢弃了横幅，还是选择将其应用程序添加到欢迎屏幕。使用数据捕获工具，您可以跟踪您的选择，并确定此功能是否对您的用户有益。

另一种隐藏技术是在文件“**”manifest . JSON“**”一个“**”query string“**”中设置开始 url，表明它是通过用户设备的启动屏幕打开的。

例如，您可以更新属性 **manifest.json** :

```
{  "name":  "Progressive Beer",  "short_name":  "beer",  "start_url":  "index.html?start=a2hs"  } 
```

更新启动 url(包括 querystring)后，您的数据分析工具将能够通过设备主屏幕上的图标确定有多少用户访问您的 PWA。

# 添加到 Homescreen 的横幅对我的网站毫无意义。如何禁用此功能？我要把这个藏起来！

通过使用一些代码，您可以复盖默认功能，并使浏览器忽略“添加到主屏幕”横幅(2HS)。通过向页面添加以下代码，您可以访问横幅提示事件并复盖默认行为:

```
window.addEventListener("beforeinstallprompt", function(e) {
  e.preventDefault();
  return false;
}); 
```

根据 web 应用程序的类型，显示此警告可能没有意义。您的网站可能包含敏感主题，甚至是短暂的活动，横幅对用户来说变得越来越烦人，而不是有用。

# 我添加到闪屏(A2HS)的功能似乎不起作用-帮助！？

好的，那么您已正确地将一个文件“**”manifest . JSON“**”添加到您的站点中，并在 HTML 的“**head”**标签中引用了该文件，如下所示:

```
<link rel="manifest" href="manifest.json"> 
```

但是，如果出于某种原因您还没有看到页面底部的横幅，您可能需要检查一些东西。首先，要在两点钟出现横幅，必须满足某些条件:

*   您的站点必须运行 HTTPS，具有有效的清单文件(带有 URL 和启动图标)，并且具有活动的“**服务工作器”**文件。
*   用户还必须在过去 5 分钟内至少访问过您的站点两次。原因是，如果横幅多次出现，可能会给用户带来很多垃圾邮件。这些“安装我们的本机应用程序”横幅在一些网站上已经坏了！

# 我正在服务工作器中为代码缓存添加资源，但更改文件时缓存未更新。即使在刷新页面后，我仍然可以看到文件的旧版本-为什么？

首先查看 **Chrome DevTools** 以确定实际缓存的文件。如果打开 Chrome Dev Tools 并访问“**应用程序”(**application)选项卡，则可以确定实际缓存了哪些文件:

[![](img/5bf0500706d48af9bdeb16aacbc5319f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4RrjGD6w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://307a6ed092846b809be7-9cfa4cf7c673a59966ad8296f4c88804.ssl.cf3.rackcdn.com/pwa-tips-tricks/service-worker-cache.jpg)

如果在进行更改时需要确保文件始终保持最新，则可以考虑为文件添加版本并重命名文件。这样可以确保每个文件更改都正确缓存。例如，使用[文件版本控制](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching#invalidating_and_updating_cached_responses)，我们可以按如下方式在 HTML 中引用 JavaScript 文件:

```
<script src=”/js/main-v2.js”> 
```

每次更改文件时，都会增大导致重新下载的版本。

确保始终获得新代码的另一种方法是，在升级后启用服务工作器时，实际上删除当前缓存条目。通过侦听服务工作进程生命周期中的激活事件，您可以清除缓存。[我建议将此链接作为指南](https://googlechrome.github.io/samples/service-worker/custom-offline-page/)。

根据 PWA 的构建方式，您可能希望选择最适合您需求的策略。

# 如何对我的服务工作人员代码进行单位测试？

测试服务工作人员代码可能很复杂，但不要害怕，Matt Gaunt 写了一篇关于服务工作人员测试详细信息的出色文章。我建议您阅读媒体上的这篇文章以了解更多信息。

# 我不确定我的 manifest.json 文件是否正常工作-我怎么测试？

我最喜欢用来验证‘t1’清单文件的工具之一是‘T2’清单验证器. app spot . com’。web 应用程序清单验证器检查文件并使用 W3C 规范来确定文件是否有效。如果您很难理解 web 应用程序清单看起来不正确的原因，该工具将提供有关导致问题的部分以及可能导致问题的各种原因的反馈。

[![](img/05e366fcae8c53b3e84997162e173fc1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l7owhIyA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://307a6ed092846b809be7-9cfa4cf7c673a59966ad8296f4c88804.ssl.cf3.rackcdn.com/pwa-tips-tricks/validator.png)

但是，如果您努力创建这些文件，发现您在这里和那里都犯了错误，我建议您使用清单文件生成器。【bruce lawson 创作的 Github 的这个 rest 有一个有用的工具，您只需输入您的详细信息并为您生成完整的清单文件即可。

# 服务工作人员档案多久更新一次？

每次导航到服务工作器范围内的新页面时，Chrome 都会对传递给调用“`navigator.serviceWorker.register()`”的 JavaScript 功能发出标准 HTTP 请求。默认情况下，此 HTTP 请求将遵循 HTTP 缓存策略，但如果服务工作器文件超过 24 小时，Chrome 将始终在网络上查找服务工作器文件的新版本。这是为了确保开发人员不会意外地实施“已损坏”的服务工作器文件，或在浏览器中永久锁定的错误文件。将其视为服务工作人员文件的安全开关。

如需更多资讯，[我建议您阅读以下堆叠溢位文章，Google 的 Jeff Posnick 将在其中深入了解](https://stackoverflow.com/questions/38843970/service-worker-javascript-update-frequency-every-24-hours/38854905#38854905)。

# 我的服务工作人员档案发出错误，但我不确定什么是错误的。怎么调试这个？

无疑，调试服务工作器代码最简单的方法是在浏览器中使用 DevTools。如果打开 Chrome 必须工具并导航到“**sources**”选项卡，则可以设置断点来帮助您调试错误。

[![](img/d9e188ca3affcd4deea16429710d63f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PURaj-cz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://307a6ed092846b809be7-9cfa4cf7c673a59966ad8296f4c88804.ssl.cf3.rackcdn.com/pwa-tips-tricks/debugging-service-worker.jpg)

在 DevTools 中设置断点后，当到达断点时，代码将暂停，这样您就可以准确地看到逻辑的运行方式。掌握 DevTools 是成为更好的开发者的一大步。虽然许多浏览器供应商都为其开发工具提供了教程，但我最喜欢 Chrome DevTools。如欲了解更多信息，[建议查阅以下链接](https://developers.google.com/web/fundamentals/getting-started/codelabs/debugging-service-workers/)。

# 我什么都试过，但出于某种疯狂的原因，我的服务工作者逻辑似乎从来没有运行过——帮助！？

值得检查一下您的 DevTools，看看您是否激活了错误的配置。例如，如果启用**【网络旁路】T1，[服务工作器]逻辑将被忽略，而搜索网络资源而不是缓存。**

[![](img/d6e51923e3a0a248812fc89d6f45d5c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OO7kEh4C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://307a6ed092846b809be7-9cfa4cf7c673a59966ad8296f4c88804.ssl.cf3.rackcdn.com/pwa-tips-tricks/bypass-for-network.jpg)

同样，您可能希望确保在不需要其他设置时未启用这些设置。例如，**离线**和**重装更新**——我反复绞尽脑汁想弄清楚为什么我的代码不起作用，只是发现我忘记禁用其中一个设置──d’oh！

# 如果用户在其主页屏幕上安装了我的 Web 应用程序，但用 Chrome 清除了缓存，那么我网站上缓存的资源也是干净的吗？

是的，由于 Chrome 启用了 Progressive Web App 体验，因此目前正在共享存储。如果用户清除 Chrome 缓存，其 PWA 也会清除其存储。

如果您想进一步了解 Chrome 中增强的 2HS 功能，[强烈建议您阅读下面的文章](https://developers.google.com/web/updates/2017/02/improved-add-to-home-screen#will_my_installed_sites_storage_be_cleared_if_the_user_clears_chromes_cache)。

# 我的渐进式 Web 应用程序可以在用户设备上使用多少内存？

诚实的回答是，这确实取决于设备和存储条件。与所有浏览器存储一样，如果设备处于存储压力下，浏览器可以自由处置。

如果要确定您拥有多少存储以及使用了多少存储，下面的代码可能会有所帮助。

```
navigator.storageQuota.queryInfo("temporary").then(function(info) {
  console.log(info.quota); // A quantidade total em bytes
  console.log(info.usage); // Quantos dados você usou até agora em bytes
}); 
```

上述代码可能无法在所有浏览器中正常工作，但肯定会指向正确的方向。[栈溢出有一个很好的响应，更详细地解释了这一点](https://stackoverflow.com/questions/35242869/what-is-the-storage-limit-for-a-service-worker)。

# 我的缓存资源似乎不时过期，如何确保它们永久缓存？

当设备上的存储空间不足时，浏览器会自动清理存储以增加可用空间。虽然这可以确保您的用户设备始终能够顺利运行，但这可能会使构建真正脱机的 Web 体验变得更加困难。

不要害怕！这是正确的。如果要使缓存更持久，可以使用一些代码明确请求。

```
if (navigator.storage && navigator.storage.persist) {
  navigator.storage.persist().then(granted => {
    if (granted) {
      alert("Armazenamento persistirá e não será limpo");
    } else {
      alert("Armazenamento não persistirá e pode ser limpo");
    }
  });
} 
```

在授予永久存储之前，有一些条件需要满足，但如果您想进一步了解这一强大功能，[建议您阅读本文以了解更多信息](https://developers.google.com/web/updates/2016/06/persistent-storage)。

# 我在服务工作人员文件中添加了处理推式通知的代码，但如何快速测试它们而无需编写服务器端代码？

如果您正在寻找在 Web 应用程序中模拟推式事件的快速方法，Chrome DevTools 提供了一种在操作中模拟推式事件的快速而简便的方法。

[![](img/1f3cee7f6b7b75a059deb841fd53d8e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GMWOL4-m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://307a6ed092846b809be7-9cfa4cf7c673a59966ad8296f4c88804.ssl.cf3.rackcdn.com/pwa-tips-tricks/simluate-push.jpg)

# 我创建了脱机 Web 应用程序，但现在看不到用户是如何使用我的应用程序的。如何跟踪使用情况？

毫无疑问，最近出现的最有趣的库之一应该是 Google Analytics 脱机包。通过在服务工作器中使用一些智能方法，库将在用户脱机时排队所有 analytics 请求，并在恢复连接后将排队的请求发送到服务器。

要开始使用库，只需使用以下代码将其包含在 service worker 文件中。

```
importScripts('../build/offline-google-analytics-import.js');                   

goog.offlineGoogleAnalytics.initialize();                           

self.addEventListener('install', () => self.skipWaiting());                 
self.addEventListener('activate', () => self.clients.claim()); 
```

通过将此代码添加到服务工作器文件中，库将跟踪用户在脱机时执行的操作，创建队列，并在用户恢复连接时按顺序发送这些操作。很酷的东西！

# 如何处理查询字符串和缓存参数？

当服务工作进程搜索缓存响应时，它使用请求 URL 作为密钥。默认情况下，请求 URL 必须与用于高速缓存响应的 URL 完全匹配，包括 URL 搜索部分中的所有 querystring 参数。

例如，如果您请求一个包含 querystring 的 URL，并且该 URL 可能与以前的查询匹配，则您可能会发现下次不会显示该查询，因为 querystring 稍有不同。要在检查高速缓存时忽略 querystring，请使用属性**【ignore research】**，并将值设置为 true。

下面的代码给出了 a 中的光盘概念

```
self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request, {
      **ignoreSearch: true**
    }).then(function(response) {
      return response || fetch(event.request);
    })
  );
}); 
```

# 完成

很多时候，我看到自己拔头发试图为服务人员找出不同的细节，只是为了发现解决方案比看起来简单——希望你发现这篇文章有用！

为了保持知识共享，[创建了 Github 存储库，其中包含所有这些问题](https://github.com/deanhume/pwa-tips-tricks)。如果你有什么想补充的建议，或者什么东西看起来不准确，请创建一个公关，我们可以一起增长这份文件——你永远不知道这对别人会有什么帮助！

# 克雷蒂托

*   [一大串进步的网络应用技巧&技巧](https://deanhume.com/a-big-list-of-progressive-web-app-tips/)，escrito origination e por[迪安·休姆](https://twitter.com/deanohume)