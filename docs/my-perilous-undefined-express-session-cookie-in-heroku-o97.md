# 我在 Heroku 的危险的未定义的饼干

> 原文：<https://dev.to/tmns/my-perilous-undefined-express-session-cookie-in-heroku-o97>

最近我一直在开发一个客户端加密密码管理器，用 React 和 Express 构建，你可以在这里用[玩一下](https://keysafeapp.herokuapp.com)。

前几天，我觉得是时候部署到 Heroku 了。像往常一样，在我的本地环境中一切都很好，但是当我试图在 prod 中运行这个应用程序时，一切都崩溃了。调试时间！！

到底是什么问题？该应用程序将建立和启动良好；然而，每当我试图导航到该应用程序，它会立即崩溃服务的标准 500 应用程序错误 Heroku 页面。我立即检查了应用程序的日志，发现了一个我以前没有发现的错误:

```
/app/node_modules/express-session/session/session.js:59
  this.cookie.maxAge = this.cookie.originalMaxAge;

TypeError: Cannot read property 'originalMaxAge' of undefined
    at Session.resetMaxAge (/app/node_modules/express-session/session/ session.js:59:36)
    at Session.touch (/app/node_modules/express-session/session/ session.js:48:15)
    at ServerResponse.end (/app/node_modules/express-session/index.js:326:21)
    at Array.write (/app/node_modules/finalhandler/index.js:297:9)
    at listener (/app/node_modules/on-finished/index.js:169:15)
    at onFinish (/app/node_modules/on-finished/index.js:100:5)
    at callback (/app/node_modules/ee-first/index.js:55:10)
    at IncomingMessage.onevent (/app/node_modules/ee-first/index.js:93:5)
    at IncomingMessage.emit (events.js:203:15)
    at endReadableNT (_stream_readable.js:1145:12)
    at process._tickCallback (internal/process/next_tick.js:63:19)
npm ERR! code ELIFECYCLE
npm ERR! errno 1 
```

Enter fullscreen mode Exit fullscreen mode

`this.cookie`就是`undefined`！怎么会？什么？什么时候？为什么？

当我在这种情况下看到 cookie 错误时，我会直接想到在 Express 中使用会话处理时容易陷入/不太正确的常见 CORS、`secure`和`proxy trust`陷阱。因此，我检查了所有相关的代码，改变了它，翻转了布尔值，等等。但是没有骰子...

接下来，我开始研究这个错误，看看是否有人遇到过同样的问题。我发现了一些相似的结果，但没有完全相同的。此外，大多数回答都建议和 CORS 之类的人一起玩——这条路我已经走得够远了。在一篇帖子中，一个人声称他们不小心将 cookie 的`maxAge`属性设置为一个或多或少会立即过期的值。我仔细检查了我在服务器的`config.js`文件中设置的值:

```
export const {
  PORT = 4000,
  PROTO = 'http',
  HOST = 'localhost',
  DB_NAME = 'keySafeDB',
  DB_URI = 'mongodb://localhost:27017/keySafeDB',
  SESS_NAME = 'sessionId',
  SESS_SECRET = 'test-secret',
  SESS_LIFETIME = 1000 * 60 * 60
} = process.env; 
```

Enter fullscreen mode Exit fullscreen mode

看起来很好！但是我也在 Heroku 的设置中将`SESS_LIFETIME`设置为一个配置变量。所以我也在那里查了一下:

```
SESS_LIFETIME = 1000 * 60 * 60 * 24 
```

Enter fullscreen mode Exit fullscreen mode

那看起来也很好！那可能是什么呢？？我决定打开`express-session`调试，看看它是否能给我提供任何有用的信息。这次用`DEBUG=express-session`启动 app，app 还打印出了这个小宝石:

```
express-session no SID sent, generating session 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这里没有太多的信息，但是也许我应该挖掘一下`express-session`的来源，看看这一行是从哪里打印出来的，应用程序在那个时候应该是什么状态，以及错误本身与这个上下文有什么确切的联系。看着`express-session/index.js`，发现这里的字符串:

```
 // generate a session if the browser doesn't send a sessionID
    if (!req.sessionID) {
      debug('no SID sent, generating session');
      generate();
      next();
      return;
    } 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，研究`generate`函数并没有给我带来任何新的启示。因为我已经在源代码中了，所以我决定在`express-session/session/session.js`中也检查错误本身的来源，但是这也没有让我看到任何突破性的东西。

所以，我很迷茫。由于我已经用尽了我能想到的所有可能的错误，我决定这是一个寻求帮助的合适时机。我在一些不和谐社区张贴了我的请求，但不幸的是，由于错误看起来如此模糊，没有人能提供任何真正的见解。

所以我决定把我的问题直接带到源代码本身，打开一个 Github 问题。我打开了新的问题形式，但写之前停了下来。我的直觉告诉我，在采取如此激烈的措施之前，我应该多考虑一下。我想，我仍然可以做的事情是删除一些我在 Heroku 的设置中设置的与 cookie 相关的配置变量，只使用我硬编码在`config.js`文件中的变量。毕竟，该应用在本地使用时工作良好，并且在不同环境之间变化的主要值是这些变量的值。

我从删除配置变量`SESS_LIFETIME`开始。然后我试图打开应用程序......

....

成功了！

但是为什么呢？？！一直都是这样吗？？Heroku 设置中的 SESS_LIFETIME 值和本地`config.js`文件中的 SESS _ LIFETIME 值之间的唯一区别是`24`的额外乘法，以使 cookie 持续一天:`1000 * 60 * 60 * 24`。我是不是忘记了如何乘法，完全是在捏造这个等式？我将`config.js`文件中的值也设置为`1000 * 60 * 60 * 24`，并重新部署。该应用程序仍然有效！！什么？！！

然后我重置了 Heroku 中的 config 变量，应用程序立即回到崩溃状态。所以最终这个小小的配置变量是罪魁祸首！哎哟！考虑到这根本没必要，我会加倍痛苦。

我查看了 Heroku 文档，没有发现任何关于配置变量值可以包含什么的限制。我还将该值设置为一个简单的整数，这也导致了应用程序崩溃。你可能会认为这将被记录在某个地方。或者可能只是众所周知 Heroku 的配置变量只能是字符串？如果有人有一些见解，将不胜感激！

但是这篇文章的要点是简单地警告任何可能看到类似错误的人——首先检查您的配置变量！它可能会节省你几天时间(咳咳..我完全是指时间..)的无效故障排除:)