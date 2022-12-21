# 在 Vue CLI 3 应用程序中使用 OneSignal

> 原文：<https://dev.to/idoshamun/using-onesignal-in-a-vue-cli-3-application-1d74>

如果您正在开发一个 web 应用程序，大多数情况下您会希望添加 web push 来吸引您的用户。

OneSignal 使管理、定制和实现网络推送变得容易，免费计划可能已经满足了您很长一段时间的需求。这就是为什么我们选择 OneSignal 作为我们内部的每日内容管理系统。

唯一的问题是 Vue CLI 的 PWA 模块的现有服务人员和 OneSignal 的 web 推送服务人员之间的冲突。我必须承认，OneSignal 在管理他们的服务人员方面不是很灵活，解决这一冲突需要一段时间。

一开始我只是尝试给 OneSignal 的服务工人添加一个简单的`importScript('/service-worker.js')`命令来导入 Vue 的服务工人。它确实工作了，但唯一的问题是它从未更新 service-worker.js 文件，将旧文件留在缓存中，并且从未显示应用程序的最新版本。😓

过了一会儿，我深入研究了 Nuxt 解决方案，找到了一个解决方案。😌

抓紧并小心跟随:

注释掉 main.js 中的以下行，因为 OneSignal 会自动注册服务工作器:

```
import './registerServiceWorker'; 
```

确保在同一个文件 main.js :
中初始化 OneSignal

```
window.OneSignal = window.OneSignal || [];
window.OneSignal.push(() => {
  window.OneSignal.init({
    appId: process.env.VUE\_APP\_ONESIGNAL,
    allowLocalhostAsSecureOrigin: process.env.NODE\_ENV !== 'production',
  });
}); 
```

记得在相关的中将 VUE 应用信号设置为你的一个信号的应用 ID。环境文件。

将 GCM 发送者属性添加到 manifest.json 文件:

```
"gcm\_sender\_id": "482941778795",
"gcm\_sender\_id\_comment": "Do not change the GCM Sender ID" 
```

现在我们必须设置 workbox 忽略 OneSignal 的文件，并且不缓存它们，将以下内容添加到您的 vue.config.js :

```
module.exports = {
  pwa: {
    workboxOptions: {
      exclude: [/OneSignal.\*\.js$/],
    },
  },
}; 
```

显然，我们还必须导入一个信号 SDK，他们建议从他们的 CDN 中获取，这样我们就可以简单地将下面一行添加到我们的 index.html:

```
<script src="https://cdn.onesignal.com/sdks/OneSignalSDK.js" async></script> 
```

现在，对于重要的部分，我们将不会使用硬编码的 OneSignal 服务工作器，但我们将在构建时生成它们，我们必须确保对于每个部署，这些服务工作器的内容将会改变，并将强制检查更新。我们可以使用构建日期时间作为每次构建都会改变的参数。下面的代码生成了两个服务工作者文件，这两个文件实际上与一个信号所要求的相同:

```
const path = require('path');
const { writeFileSync } = require('fs');

// Provide OneSignalSDKWorker.js and OneSignalSDKUpdaterWorker.js
const makeSW = (name, scripts) => {
  const workerScript = scripts.map(i => `importScripts('${i}');`).join('\r\n');
  writeFileSync(path.resolve(\_\_dirname, '../dist', name), workerScript, 'utf-8');
};

const importScripts = [
  `/service-worker.js?v=${Date.now()}`,
  'https://cdn.onesignal.com/sdks/OneSignalSDKWorker.js',
];
makeSW('OneSignalSDKWorker.js', importScripts);
makeSW('OneSignalSDKUpdaterWorker.js', importScripts); 
```

最后，我们必须在每次构建时执行这个命令，所以让我们将它添加到 package.json :
中的构建脚本中

```
"build": "vue-cli-service build && node build/onesignal.js", 
```

现在，workbox 和 OneSignal 从此可以幸福地生活在一起，并且您可以通过出色的网络推送吸引您的受众。

* * *