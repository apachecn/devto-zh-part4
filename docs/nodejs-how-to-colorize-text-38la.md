# node.js 如何给文本着色

> 原文：<https://dev.to/miku86/nodejs-how-to-colorize-text-38la>

## [T1】简介](#intro)

所以我们[在我们的机器上安装了 NodeJS](https://dev.to/miku86/what-is-nodejs-how-to-install-it-1900) 。

我们也知道[如何获得外部包](https://dev.to/miku86/nodejs-how-to-get-external-packages-npm-56oj)。

现在我们想学习如何用粉笔给文本上色。

## 写一个简单的脚本

*   打开你的终端
*   创建一个名为`index.js`的文件:

```
touch index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   将这段 JavaScript 代码添加到其中:

```
// import chalk
const chalk = require('chalk');

// Example 1: inline styling, blue background and underline
console.log(chalk.bgBlue.underline('Example 1'));

// Example 2: create reusable style 
const success = chalk.bgGreen.black.underline;
console.log(success('Example 2'));

// Example 3: you can do a lot of stuff, e.g. using hex values and template literals
const customize = chalk.hex('#B4DA55').bold;
console.log(`This is ${customize('Example 3')}.`); 
```

Enter fullscreen mode Exit fullscreen mode

注意:粉笔有很多可用的样式，阅读[粉笔](https://www.npmjs.com/package/chalk#styles)的文档。

* * *

## 从终端运行它

*   运行它:

```
node index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   结果:

[![Alt Text](img/0aa8a37c4da4054f07e16f9c82e6b878.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6s1TWjhN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sbx0oj2ipy33i1oq5jqy.png)

* * *

## 进一步阅读

*   [NPM 上的粉笔](https://www.npmjs.com/package/chalk)
*   [在 npm 上使用粉笔](https://www.npmjs.com/package/chalk#usage)

* * *

## 提问

*   你最喜欢的给 Node 中的文本着色的方法/包是什么？你为什么用它？