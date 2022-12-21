# node.js 如何创建自己的模块

> 原文：<https://dev.to/miku86/nodejs-how-to-create-your-own-module-ion>

## [T1】简介](#intro)

所以我们[在我们的机器上安装了 NodeJS](https://dev.to/miku86/what-is-nodejs-how-to-install-it-1900) 。

现在我们想学习如何创建我们自己的[模块](https://nodejs.org/api/modules.html)。

## 写一个简单的脚本

*   打开你的终端
*   创建一个名为`logger.js`的文件:

```
touch logger.js 
```

Enter fullscreen mode Exit fullscreen mode

*   将这段 JavaScript 代码添加到其中:

```
// the function should get a message type and a message
function logger(type, message) {
  let format;

  // different message for different message type
  switch (type) {
    case 'error':
      format = `[ERROR] ${message}`;
      break;
    case 'success':
      format = `[SUCCESS] ${message}`;
      break;
    default:
      format = `${message}`;
      break;
  }

  console.log(format);
}

// export the function using object property shorthand syntax
// to rename, use "newName: logger"
module.exports = { 
  logger
}; 
```

Enter fullscreen mode Exit fullscreen mode

注意:为了简单起见，这个例子是非常轻量级的，没有错误/边缘情况处理(例如，没有类型)，没有单独的消息类型文件，没有颜色等等。

* * *

## 编写第二个简单的脚本

*   打开你的终端
*   创建一个名为`index.js`的文件:

```
touch index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   将这段 JavaScript 代码添加到其中:

```
// import the exported logger property
const { logger } = require('./logger.js');

// use the function
logger('error', 'This is an error message.');
logger('success', 'This is a success message'); 
```

Enter fullscreen mode Exit fullscreen mode

## 从终端运行它

*   运行它:

```
node index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   结果:

```
[ERROR] This is an error message.
[SUCCESS] This is a success message. 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 下一步

*   问:当你忘记添加一个`type`时会发生什么？你如何解决这个问题？
*   问:如何通过将消息类型分成一个常量来改进这个例子？
*   问:如何通过使用一个对象而不是两个字符串作为参数来改进这个例子？
*   问:你需要一些额外的错误处理吗？(= >文档)

* * *

## 进一步阅读

*   [模块](https://nodejs.org/api/modules.html)
*   [模块导出](https://nodejs.org/api/modules.html#modules_module_exports)
*   [导出快捷方式](https://nodejs.org/api/modules.html#modules_exports_shortcut)

* * *

## 提问

*   你最喜欢的改善你的开发者生活的自编模块是什么？