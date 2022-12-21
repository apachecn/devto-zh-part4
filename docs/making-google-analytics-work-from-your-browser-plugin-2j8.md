# 让谷歌分析在你的浏览器插件上工作

> 原文：<https://dev.to/kambanthemaker/making-google-analytics-work-from-your-browser-plugin-2j8>

由于安全限制和协议冲突，如果你想让谷歌分析从你的浏览器插件工作，它可能不会工作。要解决这个问题，请遵循下面解释的步骤，

## 清单

第一步是让您的清单文件允许 GA 域。

```
"content_security_policy": "script-src 'self' https://www.google-analytics.com; object-src 'self'" 
```

## 安装 GA

```
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)})(window,document,'script','https://www.google-analytics.com/analytics.js','ga');ga('create', 'UA-YOUR_ID-1', 'DOMAIN_NAME');ga('set', 'checkProtocolTask', null);ga('send', 'pageview', 'user'); 
```

行很重要，因为它使 GA 忽略协议检查。这给我带来了很多麻烦。看这里[谷歌分析](https://developers.google.com/analytics/devguides/collection/analyticsjs/tasks)

域名也很重要，因为 GA 不能检测你的插件。将它设置为您想要跟踪的域名！

## 调试

GA 有一个隐藏的宝石，而不是`https://www.google-analytics.com/analytics.js`只是使用`https://www.google-analytics.com/analytics_debug.js`，它将在 chrome 控制台上打印有用的调试信息！

[![GA](img/027562d95a93f63ae2e30ddb4af52f19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uTRutEaB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/XVv0gVk/Screen-Shot-2019-06-25-at-8-59-16-AM.png%2522%2520alt%3D%2522Screen-Shot-2019-06-25-at-8-59-16-AM%2522)

参考[调试](https://developers.google.com/analytics/devguides/collection/analyticsjs/debugging)