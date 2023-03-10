# Qvault 在电子方面的离线模式

> 原文：<https://dev.to/wagslane/qvault-s-offline-mode-in-electron-4ki2>

准备在 Qvault 中加入比特币和加密货币密钥生成，刚刚加入的功能之一就是“离线模式”。现在在应用程序的顶部有一个拨动开关，当关闭时，确保应用程序不会发出网络请求。

[![](img/2dda4e7e60d895007da84b6bde8b3f2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yMwdFL1D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qvault.io/wp-content/uploads/2019/08/screenshotOffline-1024x580.png)

Qvault 一直可以离线使用，假设用户选择不注册 Qvault 的云备份选项。现在，即使用户将其加密的 vault 文件存储在 Qvault 服务器上，用户也可以选择离线并暂时在本地工作。

## 它是如何工作的？

Electron 有一个 API，允许开发者拦截和修改 web 请求。通过在使用 onBeforeRequest 钩子发出请求之前拦截所有的 web 请求，我们可以检查它们，看看我们是否想要允许每个请求。例如:

```
// callback({cancel: true}) stops the request before it is sent.
// callback({cancel: false}) sends the webRequest

mainWindow.webContents.session.webRequest.onBeforeRequest({
    // Intercept all webRequests
    urls: ['<all_urls>']
  }, (details, callback) => {
    // allow all filesystem calls
    if (details.url.substring(0, details.url.indexOf(':')) == 'file'){
      callback({cancel: false});
      return;
    }
    // don't allow any network calls if in offlineMode
    if (!onlineMode){
      callback({cancel: true});
      return;
    }
    // allow al other network calls
    callback({cancel: false});
  }); 
```

Enter fullscreen mode Exit fullscreen mode

希望这能帮助其他人在电子版中控制 web 请求，并帮助我们的用户理解 Qvault 中的“离线模式”!感谢阅读。

由[莱恩·瓦格纳](https://medium.com/@lane.c.wagner)

电子公司 API 的文档:[https://Electron js . org/docs/API/web-request # webrequestonbeforerequestfilter-listener](https://electronjs.org/docs/api/web-request#webrequestonbeforerequestfilter-listener)

链接到我们的实现:[https://github . com/Q-Vault/Q Vault/blob/master/main/setupwrequests . js](https://github.com/Q-Vault/qvault/blob/master/main/setupWebRequests.js)

下载 q vault:[https://q vault . io](https://qvault.io)

星我们的 Github:[https://github.com/Q-Vault/qvault](https://github.com/Q-Vault/qvault)

帖子 [Qvault 在电子](https://qvault.io/2019/08/27/qvaults-offline-mode-in-electron/)的离线模式最早出现在 [Qvault](https://qvault.io) 上。