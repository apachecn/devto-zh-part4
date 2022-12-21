# 带有 QCObjects 的渐进式 Web 应用程序

> 原文：<https://dev.to/qcobjects/progressive-web-apps-with-qcobjects-30pl>

网络应用程序开发需求巨大。学习曲线变得过于陡峭。开发人员正面临着比以往任何时候都更快地产生高质量代码的挑战。

即使是现在，尽管设备原生应用程序本身在增长，但 web 应用程序“必须像”原生应用程序的传统思想已经越来越被社区接受。这就是为什么渐进式网络应用程序确实存在。

QCObjects 在一个更安全、更快速、更容易学习的运行时组件范围内集成了许多工具，所以为什么不使用它来制作一个引人注目的渐进式 web 应用程序，该应用程序也可以设计成 N 层架构，并且所有这些都使用纯 javascript？

看，如果你想做一个专业的 web 应用程序，你需要首先考虑这三个基本问题:

*   表演
*   易接近
*   最佳实践

那么，我们来谈谈性能:

浏览器工程师建议页面包含少于 1500 个 DOM 节点。最佳点是树的深度< 32 elements and fewer than 60 children/parent element. A large DOM can increase memory usage, cause longer style calculations, and produce costly layout reflows. Learn more about this [这里是](https://developers.google.com/web/tools/lighthouse/audits/dom-size)。
QCObjects 的设计思路就在此。这就是为什么您可以将一个对象添加到 DOM 树中，使得动态重建嵌套组件成为可能。这将大大减少 PWA 中 DOM 节点的深度。

为了提高性能，您可以编写一个使用缓存 api 离线工作的服务工作器，而不是一次又一次地获取内容。QCObjects 在 PWA 加载时注册服务工作者，您只需要通过键入相应的配置集来指定服务工作者 uri。

为了进一步提高性能，QCObjects 有一个复杂的缓存控制，允许您将任何“stringified”对象保存到 localStorage 并取回它。组件有一个基于复杂缓存控件的内置缓存控件，因此您可以让 QCObjects 为所有组件使用缓存(Components.cached=true)，或者为每个组件实例单独设置缓存属性。

可访问性呢？

为了使你的专业 PWA 可访问，有时你需要重复很多代码。使用 QCObjects，您可以将代码组织成组件，并在运行时加载到 PWA 中。每个组件都有一个独立的构建行为，因此您可以在 DOM 之外构建和加载组件，但是即使在这样做之后，您也可以使用 QCObjects 注入到 DOM 原型中的 append 方法将组件的主体添加到 DOM 中。例如:通过这种方式，您可以用动态数据填充一个包含 PWA 信息元标记的组件。当你想在服务器中保存元信息以对一堆已经发布的应用程序进行更改，并且你不想只为了更改应用程序描述而制作新版本时，这将使你感到高兴。

好了，让我们继续进行最佳实践:

1.-使用良好的布局。

有了 QCObjects，你可以使用任何最著名的 CSS3 框架来引导你的网页设计。但是接下来，你想做你自己的定制。为此，您可以使用 SourceCSS QCObjects 类，这对于“动态”导入文档头部的 CSS 文件非常有用。

1.-创建您的第一个应用程序

要使用 QCObjects 创建您的第一个 PWA 应用程序，您只需在控制台中键入以下内容:

```
> npm i qcobjects-cli 
```

然后，为你的应用程序创建一个目录并输入:

```
> mkdir mynewapp && cd mynewapp 
```

最后，使用 cli 工具
生成新的应用程序模板

```
> sudo qcobjects create mynewapp --pwa 
```

完成了。

2.-通过 HTTP2 提供服务

一旦创建了应用程序，就可以使用与 cli 工具一起安装的 qcobjects-server 工具来服务它。转到应用程序目录并键入:

```
> qcobjects-server 
```

然后按回车键。

然后，转到 web 浏览器并导航至:

[https://localhost](https://localhost)

3.-注册服务人员:

要使用 QCObjects 注册服务人员，您只需指定配置设置。在您的 init.js 中，放入下面一行:

```
 CONFIG.set('serviceWorkerURI','/sw.js'); 
```

这意味着 QCObjects 将在您的应用程序的根目录中查找/sw.js，并将注册为服务人员。可以使用 navigator . service worker . ready . then(...)在加载服务工作器后附加任何代码。

```
navigator.serviceWorker.ready.then(function (){
   logger.info('service worker is done');
}) 
```

5.-添加推送通知:

改编官方代码 [Mozilla](https://developer.mozilla.org/en-US/docs/Web/API/PushManager#Example)

```
 Class('PushNotifications',InheritClass,{
    _new_:function (){
      window.onpush = function(event) {
        console.log(event.data);
        // From here we can write the data to IndexedDB, send it to any open
        // windows, display a notification, etc.
      }

      navigator.serviceWorker.ready.then(
        function(serviceWorkerRegistration) {
          serviceWorkerRegistration.pushManager.subscribe().then(
            function(pushSubscription) {
              console.log(pushSubscription.endpoint);
              // The push subscription details needed by the application
              // server are now available, and can be sent to it using,
              // for example, an XMLHttpRequest.
            }, function(error) {
              // During development it often helps to log errors to the
              // console. In a production environment it might make sense to
              // also report information about errors back to the
              // application server.
              console.log(error);
            }
          );
        });
    }
  }) 
```

要激活该类，请使用以下命令:

```
let pushNotifications = New(PushNotifications); 
```

请在评论中告诉我如何改进这篇文章！

干杯！