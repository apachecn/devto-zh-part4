# TIL: Node.js 有一个内置的调试日志方法

> 原文：<https://dev.to/stefanjudis/til-node-js-has-a-built-in-debug-log-method-1kc9>

今天我在推特上看到了 [@ThisIsMisEm](https://mobile.twitter.com/ThisIsMissEm) 和 [@davidmarkclem](https://mobile.twitter.com/davidmarkclem) 之间的一段简短对话，它揭示了一个关于 Node.js 的有趣事实

虽然数百万个包依赖于非常受欢迎的[调试包](https://www.npmjs.com/package/debug)，但事实证明 Node 通过 [`util.debuglog`](https://nodejs.org/api/util.html#util_util_debuglog_section) 内置了类似的功能。使用它，你也许可以摆脱你的应用程序中的一个依赖。

```
// index.js
const util = require('util');
const debuglog = util.debuglog('app');

debuglog('hello from my debugger [%d]', 123); 
```

Enter fullscreen mode Exit fullscreen mode

当您在终端中运行这段代码时，您不会看到任何东西。但是，当您设置环境变量`NODE_ENV=app`时，会出现日志消息:

```
$ NODE_DEBUG=app node index.js
APP 86155: hello from my debugger [123] 
```

Enter fullscreen mode Exit fullscreen mode

`util.debuglog`甚至支持通配符(`*`)。

```
// index.js
const util = require('util');
const logGeneral = util.debuglog('app-general');
const logTimer = util.debuglog('app-timer');
const delay = 500;

logGeneral('Kicking off the app');

setTimeout(() => {
  logTimer('timer fired after %d', delay);
}, delay); 
```

Enter fullscreen mode Exit fullscreen mode

运行带有`app-*`环境变量的脚本会导致以下结果:

```
$ NODE_DEBUG=app-* node index.js
APP-GENERAL 86188: Kicking off the app
APP-TIMER 86188: timer fired after 500 
```

Enter fullscreen mode Exit fullscreen mode

`NODE_DEBUG`环境变量也可以用来从 Node.js 内部获取调试消息。您可能偶尔会在 Node.js 文档中遇到它。

了解`util.debuglog`很好，但是正如[大卫指出](https://mobile.twitter.com/davidmarkclem/status/1147275278273658881)，它并没有涵盖`debug`的所有功能。`debug`比如给你的日志信息涂上漂亮的颜色。丢失的颜色对你来说可能不是一个障碍，但是对于很多调试大型应用程序的人来说，它们是非常受欢迎的。

对我来说，在较小的项目和脚本中，`util.debuglog`将是`debug`的一个很好的替代方案。