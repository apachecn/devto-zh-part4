# Github 工程师解释如何优雅地放弃 IE 支持

> 原文：<https://dev.to/chantastic/a-github-engineers-explains-how-to-drop-ie-support-gracefully-jlf>

GitHub [去年夏天停止了对 ie 浏览器](https://twitter.com/michlbrmly/status/981855020948877312)的支持。
我问一个 GitHubber，团队应该知道什么才能成功地将他们的用户从 IE 中转移出来。

戴维·格拉汉姆用这些建议回应了我的呼吁。如果你觉得他们有帮助，在 [Twitter](https://twitter.com/davidgraham) 上感谢他，或者向 [BlackGirlsCode](http://www.blackgirlscode.com/donations.html) 捐款。

## 渲染支持横幅服务器端

[![IE Support Banner](img/bb72157efdb0a0c2a6703048d26c7814.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AEBD-qZ4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ek1nnl7xxcqgpqp607zj.png)

GitHub 的支持横幅是服务器端渲染的。它被渲染成七边形，以确保被看到。
糟糕的 JavaScript 会停止脚本执行，阻止代码运行。
所以客户端呈现的消息可能永远不会被执行。

如果您试图通知用户他们的浏览器不支持您需要的 JavaScript，这是很糟糕的。需要客户端消息的用户可能看不到它。

在服务器端呈现支持横幅。

## [T3 检查`User-Agent`字符串是否为`Trident`](#check-the-raw-useragent-endraw-string-for-raw-trident-endraw-)

**三叉戟**是为 IE 提供动力的引擎。
检查`Trident`请求头的[用户代理](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)字符串。

如何检查`user-agent`取决于您的服务器技术。
以下是我们所做工作的简短版本:

#### 轨道/红宝石

```
 request.user_agent =~ /Trident/ 
```

Enter fullscreen mode Exit fullscreen mode

使用[用户代理](https://rubygems.org/gems/useragent/versions/0.16.7) gem 获得额外的检测支持。

### Express/NodeJS

```
 let http = require('http'),
      server = http.createServer(function(request) {
          let isIE = /Trident/.test(request.headers['user-agent']);
      }); 
```

Enter fullscreen mode Exit fullscreen mode

将 [express-useragent](https://www.npmjs.com/package/express-useragent) 中间件用于 Express/NodeJS 应用程序。

### 浏览器

```
 /Trident/.test(navigator.userAgent); 
```

Enter fullscreen mode Exit fullscreen mode

## 提防爱管闲事的浏览器扩展

有些人有修改他们的`user-agent`字符串的浏览器扩展。
这些扩展通常用于访问那些*【IE 最佳】*的旧应用。

不幸的是，这意味着`user-agent`嗅探策略可能会产生误报。

为了对抗这些扩展，在客户机上做一些特性检测。
比如:
IE 不支持`navigator.mediaDevices`。
如果存在，你的用户浏览器可能在对你撒谎。

让服务器渲染的横幅运行一些客户端检查。
如果你怀疑某个浏览器插手，触发一个支持标签，并逐个处理这些情况。

## 不要在第一天就放弃你的福利

对你的用户要有耐心。不要太急于把他们的毯子拉出来。

在截止日期后继续提供支持。您的用户会感谢这种额外的关怀。

🕸 [chantastic](https://twitter.com/chantastic)

我相信你有识别和迁移 IE 用户的技巧和诀窍。
你**是怎么做到的？**