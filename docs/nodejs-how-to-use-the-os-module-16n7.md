# NodeJS:如何使用操作系统模块

> 原文：<https://dev.to/miku86/nodejs-how-to-use-the-os-module-16n7>

## [T1】简介](#intro)

所以我们[在我们的机器上安装了 NodeJS](https://dev.to/miku86/what-is-nodejs-how-to-install-it-1900) 。

现在我们想学习如何通过使用 [OS 模块](https://nodejs.org/api/os.html)来获取关于操作系统的信息。

## 写一个简单的脚本

*   打开你的终端
*   创建一个名为`index.js`的文件:

```
touch index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   将这段 JavaScript 代码添加到其中:

```
const { platform, arch, release, totalmem, freemem } = require('os');

console.log(`Your Operating System: ${release()}  ${platform()}  ${arch()}`);
console.log(`${((freemem() / totalmem()) * 100).toFixed(2)} % of your RAM is free.`); 
```

Enter fullscreen mode Exit fullscreen mode

注意:我使用最常用的 url 属性来降低这个简单例子的复杂性。要查看所有可用的属性，请阅读操作系统模块的文档。有很多很酷的东西。

* * *

## 每行解释

```
/*
  import the os module & destructure the desired properties/functions
  similar to:
  const os = require('os');
  const { platform, arch, release, totalmem, freemem } = os;
*/ 
const { platform, arch, release, totalmem, freemem } = require('os');

// log some information about the operating system
console.log(`Your Operating System: ${release()}  ${platform()}  ${arch()}`);

// log some information about the memory (ram) (number is rounded to two decimals)
console.log(`${((freemem() / totalmem()) * 100).toFixed(2)} % of your RAM is free.`); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 从终端运行它

*   运行它:

```
node index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   结果:

```
Your Operating System: 5.2.9-arch1-1-ARCH linux x64
18.63 % of your RAM is free. 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 进一步阅读

*   [操作系统模块](https://nodejs.org/api/os.html)
*   [操作系统属性概述](https://nodejs.org/api/os.html#os_os)

* * *

## 提问

*   你有没有一个有趣的想法，我们可以用这个模块创造什么？