# 将任何 Jekyll 站点变成 PWA

> 原文：<https://dev.to/thewhitewulfy/make-any-jekyll-site-into-a-pwa-1de2>

渐进式网络应用程序(PWA)使用现代网络功能来提供类似应用程序的用户体验。任何网站都可以做成 PWA。

## 基础知识

将一个基本的网站转变成 PWA 很容易，也有很多好处，但是首先，让我们简单地了解一下它们。

其核心的渐进式网络应用程序只是一种优化您的网站，以更好，更快地交付。作为开发者，我们应该学会利用这些新的可能性，不管我们的内容是什么。

我自己的网站只是一堆静态 HTML，我的博客基于 Jekyll，它们仍然是完全有效的渐进式网络应用程序。如果你在网上运行任何东西，你肯定能从中受益。

美妙之处在于，pwa 提供了两个世界的最佳选择——来自 web 的深度链接和 URL、离线访问、推送通知和来自本机应用的更多设备特定功能——同时仍然保持完全的平台独立性。只有网络。如果老一点的浏览器不支持，就不会坏；只是回落到默认:正规网站。是的，有一些服务可以将你的 PWA 制作成应用程序，你可以发布到应用程序商店。

有一些重要的优势:

*   更快、更安全的用户体验
*   更好的谷歌排名
*   更好的可用性
*   更好的性能
*   办公室访问
*   像本地应用程序一样的主屏幕快捷方式

即使你不希望你的用户“安装”你的 PWA(例如，在他们的主屏幕上放置一个快捷方式)，进行切换仍然有很多好处。事实上，制作 PWA 的所有必要步骤都会积极地改善你的网站，其中许多步骤被认为是最佳实践。

## 我在 Jekyll 怎么做？

我们将从清单开始，然后转到 JavaScript 部分。Jekyll 使用了一个基于 partials 的构建系统，我们将利用这一点。

本系列的第二部分将介绍如何让任何静态网站成为 PWA，第三部分将讨论如何在 PWA 中使用特定于设备的功能。

在本教程中，我们将部分利用 [PWA-Builder](https://www.pwabuilder.com) 来做许多事情。

## 载货单

清单只是一个 JSON 文件，它描述了 PWA 的所有元数据。比如你的应用程序的名称、语言和图标都在里面。这些信息将告诉浏览器如何在应用程序保存为快捷方式时显示它。

```
{  "lang":  "en",  "dir":  "ltr\rtl",  "name":  "This is my jekyll PWA",  "short_name":  "myPWA",  "icons":  [  {  "src":  "\/assets\/images\/touch\/android-chrome-192x192.png",  "sizes":  "192x192",  "type":  "image\/png"  }  ],  "theme_color":  "#1a1a1a",  "background_color":  "#1a1a1a",  "start_url":  "/",  "display":  "standalone",  "orientation":  "natural"  } 
```

这通常被称为`manifest.json`或`site.webmanifest`，从您站点的`<head>`链接到

```
<link rel="manifest" href="manifest.json"> 
```

关于清单文件中可以有哪些字段，请参见 [MDN](https://developer.mozilla.org/en-US/docs/Web/Manifest) 。

对于 jekyll，我们将对`manifest.json`做一些更改，这样我们可以直接从`_config.yml`进行更改，并防止 Jekyll 使用默认布局来呈现它(这可能会将 json 插入到某些主题的 html 中)。

`<head>`部分将出现在构成每页标题的`partial`中。通常它会位于`_partials`。

```
 ---  layout:  none  ---  {  "lang":  "{{ site.language }}",  "dir":  "{{ site.lang_direction }}",  "name":  {{  site.name  |  smartify  |  jsonify  }},  "short_name":  {{  site.short_name  |  smartify  |  jsonify  }},  "icons":  [  {  "src":  "\/assets\/images\/touch\/android-chrome-192x192.png",  "sizes":  "192x192",  "type":  "image\/png"  }  ],  "theme_color":  "{{ site.color }}",  "background_color":  "{{ site.color }}",  "start_url":  "{{ site.url }}",  "display":  "standalone",  "orientation":  "natural"  } 
```

在`_config.yml`中，我们可能已经有了下面这些字段，如果没有，我们可以随时在那里添加。😅

```
 name: "mysite"
short_name: "ms"
url: "https://mysite.com"
language: "en"
lang_direction: "ltr"
color: "#abc123" 
```

你的货单已经准备好了。现在我们进入下一部分。

## 登记服务人员

现在我们需要将注册服务人员的 JavaScript 添加到构成每个页面的`<head>`的`partial`中。和以前一样，它通常位于`_partials`。

我们需要在`<script>`标签中添加下面这段 JavaScript 代码。

```
 if ("serviceWorker" in navigator) {
  if (navigator.serviceWorker.controller) {
    console.log("An active service worker found, no need to register");
  } else {
    // Register the service worker
    navigator.serviceWorker
      .register("{{ site.baseurl }}/serviceworker.js", {
        scope: "./"
      })
      .then(function (reg) {
        console.log("Service worker has been registered for scope: " + reg.scope);
      });
  }
} 
```

## 服务人员

现在我们进入下一步。我们需要在你的站点的根目录下创建`serviceworker.js`。

它将具有以下 JavaScript:

```
 const CACHE = "pwabuilder-offline";

const offlineFallbackPage = "index.html";

// Install stage sets up the index page (home page) in the cache and opens a new cache
self.addEventListener("install", function (event) {
  console.log("Install Event processing");

  event.waitUntil(
    caches.open(CACHE).then(function (cache) {
      console.log("Cached offline page during install");

      if (offlineFallbackPage === "ToDo-replace-this-name.html") {
        return cache.add(new Response("Update the value of the offlineFallbackPage constant in the serviceworker."));
      }

      return cache.add(offlineFallbackPage);
    })
  );
});

// If any fetch fails, it will look for the request in the cache and serve it from there first
self.addEventListener("fetch", function (event) {
  if (event.request.method !== "GET") return;

  event.respondWith(
    fetch(event.request)
      .then(function (response) {
        console.log("Add page to offline cache: " + response.url);

        // If request was success, add or update it in the cache
        event.waitUntil(updateCache(event.request, response.clone()));

        return response;
      })
      .catch(function (error) {        
        console.log("Network request Failed. Serving content from cache: " + error);
        return fromCache(event.request);
      })
  );
});

function fromCache(request) {
  // Check to see if you have it in the cache
  // Return response
  // If not in the cache, then return error page
  return caches.open(CACHE).then(function (cache) {
    return cache.match(request).then(function (matching) {
      if (!matching || matching.status === 404) {
        return Promise.reject("no-match");
      }

      return matching;
    });
  });
}

function updateCache(request, response) {
  return caches.open(CACHE).then(function (cache) {
    return cache.put(request, response);
  });
} 
```

现在您已经完成了 JavaScript 部分。

## 向前看

把这些改变放到你的 Jekyll 网站上，瞧！它现在是一个 PWA。😁

为了简单起见，我故意忽略了将图标映射到配置的部分，如果你需要帮助，请在下面给我留言，我会告诉你怎么做。

此外，HTTPS 是必不可少的网站是一个 PWA。很可能你已经听说过它，也许正在使用它，所以我没有强调它。

[可选]您可以通过 Lighthouse Audit 检查 PWA 的状态。😄

## 制作你的 app

是啊！

如果你已经走了这么远，为什么不走得更远呢？

如果你愿意，你可以通过 [PWA-Builder](https://www.pwabuilder.com) 将你的应用上传到微软商店、Play 商店和应用商店。

首先，你必须输入你的网站的网址，然后点击回车。它会给你的 PWA 打满分。如果你遵循这个指南，你会得到 95 分。还不错。😅

你会在右上角看到一个亮紫色的按钮，会出现一个页面，上面有下载应用程序并继续下一步操作的说明。如果你需要帮助，你可以在下面留言。😀

##### 下次见！

干杯！🍻