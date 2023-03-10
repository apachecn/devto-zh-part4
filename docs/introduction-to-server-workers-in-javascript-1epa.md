# JavaScript 中的服务人员简介

> 原文：<https://dev.to/attacomsian/introduction-to-server-workers-in-javascript-1epa>

*本文最初发表于[attacomsian.com/blog](https://attacomsian.com/blog/service-workers-javascript)。*

* * *

服务人员是渐进式网络应用的核心部分，这些应用允许缓存资源和[网络推送通知](https://attacomsian.com/blog/desktop-notifications-javascript)等，以创建有效的离线体验。它们充当 web 应用程序、浏览器和网络之间的代理，允许开发人员拦截和缓存网络请求，并根据网络的可用性采取适当的措施。

一个服务工作者在一个单独的线程上运行，所以它是非阻塞的。这也意味着它不能访问主 JavaScript 线程中可用的 [DOM](https://attacomsian.com/blog/getting-dom-elements-javascript) 和其他 API，如 cookies、XHR、web 存储 API(本地存储和会话存储)等。因为它们被设计成完全异步的，所以它们大量使用[承诺](https://attacomsian.com/blog/promises-javascript)来等待网络请求的响应。

> 出于安全考虑，服务人员只能在 HTTPS 上运行，不能在私人浏览模式下使用。然而，在进行定位请求时，您不需要安全的连接(对于测试来说已经足够了)。

## 浏览器支持

服务工作器是一个相对较新的 API，只受现代网络浏览器的支持。因此，我们首先需要检查浏览器是否支持该 API:

```
if('serviceWorker' in navigator) {
    // Supported 😍
} else {
    // Not supported 😥
} 
```

## 服务人员登记

在我们开始缓存资源或拦截网络请求之前，我们必须在浏览器中安装一个服务工作器。因为服务工作者本质上是一个 JavaScript 文件，所以可以通过指定文件的路径来注册它。该文件必须可以通过网络访问，并且应该只包含服务人员代码。

您应该等到页面加载完毕，然后将服务工作者文件路径传递给`navigator.serviceWorker.register()`方法:

```
window.addEventListener('load', () => {
    if ('serviceWorker' in navigator) {
        // register service worker
        navigator.serviceWorker.register('/sw-worker.js').then(
            () => {
                console.log('SW registration succesful 😍');
            },
            err => {
                console.error('SW registration failed 😠', err)
            });
    } else {
        // Not supported 😥
    }
}); 
```

您可以在每次加载页面时运行上述代码，而不会遇到任何麻烦；浏览器将决定服务人员是否已经安装，并相应地进行处理。

## 服务人员生命周期

注册生命周期包括三个步骤:

1.  [计] 下载
2.  安装
3.  使活动

当用户第一次访问您的网站时，会立即下载服务人员文件并尝试安装。如果安装成功，服务人员将被激活。在用户访问另一个页面或刷新当前页面之前，服务人员文件中的任何功能都不可用。

## 浏览器事件

一旦安装并激活了服务工作器，它就可以开始拦截网络请求并缓存资源。这可以通过监听服务工作器文件中浏览器发出的事件来实现。浏览器发出以下事件:

*   安装维修工人时发出`install`。
*   `activate`在服务人员成功注册和安装后发送。此事件可用于在安装新版本之前移除过时的缓存资源。
*   每当网页请求网络资源时发出`fetch`。它可以是任何东西:一个新的 HTML 文档、一个图像、一个 JSON API、一个样式表或 JavaScript 文件，任何远程位置上可用的东西。
*   当接收到新的推送通知时，由推送 API 发送。您可以使用这个事件向用户显示一个通知。
*   连接断开后，当浏览器检测到网络可用时，调用`sync`。

## 提供缓存资源

当服务人员正在安装缓存特定资源时，我们可以监听`install`事件，当我们不在网络中时，将需要这些资源来为页面提供服务:

```
const CACHE_NAME = 'site-name-cache';

self.addEventListener('install', event => {
    event.waitUntil(
        caches
            .open(CACHE_NAME)
            .then(cache =>
                cache.addAll([
                    'favicon.ico',
                    'projects.json',
                    'style.css',
                    'index.js',
                    'https://fonts.googleapis.com/css?family=Open+Sans:400,700'
                ])
            )
    );
}); 
```

上面的示例代码使用缓存 API 将资源存储在名为`site-name-cache`的缓存中。

> `self`是一个只读的全局属性，服务人员使用它来访问自己。

现在让我们监听一个`fetch`事件来检查所请求的资源是否已经存储在缓存中，如果找到就返回:

```
// ...
self.addEventListener('fetch', event => {
    event.respondWith(
        caches.match(event.request).then(response => {
            if (response) {
                //found cached resource
                return response;
            }
            return fetch(event.request);
        })
    );
}); 
```

我们寻找由`request`属性标识的资源的缓存条目，如果没有找到，我们发出一个[获取请求](https://attacomsian.com/blog/javascript-fetch-api)来获取它。如果您也想缓存新的请求，您可以通过处理 fetch 请求的响应，然后将其添加到缓存中，如下所示:

```
//...
self.addEventListener('fetch', event => {
    event.respondWith(
        caches.match(event.request).then(response => {
            if (response) {
                //found cached resource
                return response;
            }

            // get resource and add it to cache
            return fetch(event.request)
                .then(response => {
                    // check if the response is valid
                    if (!response.ok) {
                        return response;
                    }

                    // clone the response
                    const newResponse = response.clone();

                    // add it to cache
                    caches.open(CACHE_NAME)
                        .then(cache =>
                            cache.put(event.request, newResponse)
                        );

                    // return response
                    return response;
                });
        })
    );
}); 
```

## 服务人员更新

安装服务工作程序后，它会继续运行，直到被用户删除或更新。要更新服务人员，您只需在服务器上上传服务人员文件的新版本。当用户访问你的网站时，浏览器会自动检测到文件的变化(即使只有一个字节就足够了)，并安装新版本。

就像第一次安装一样，只有当用户导航到另一个页面或刷新当前页面时，新的服务人员功能才可用。

我们能做的一件事是监听`activate`事件并移除旧的缓存资源。下面的代码通过遍历所有缓存并删除与我们的缓存名称相匹配的缓存来实现这一点:

```
// ...
self.addEventListener('activate', event => {
    event.waitUntil(
        caches.keys().then(keys => {
            return Promise.all(
                keys.map(cache => {
                    if (cache === CACHE_NAME) {
                        return caches.delete(cache);
                    }
                })
            );
        })
    );
}); 
```

服务人员介绍完毕。如果你想了解更多，请查阅[service worker Cookbook](https://serviceworke.rs/)——这是一个在现代网站中使用服务工作者的实用示例集。

* * *

✌️:我写了关于现代 JavaScript、Node.js、Spring Boot 和所有 web 开发的东西。 [**订阅我的简讯**](https://attacomsian.com/newsletter) 每周获取 web 开发教程& protips。

* * *

**喜欢这篇文章？** [在推特上关注@ attacomsian](https://twitter.com/attacomsian)。你也可以在 [LinkedIn](https://linkedin.com/in/attacomsian) 和 [DEV](https://dev.to/attacomsian) 上关注我。