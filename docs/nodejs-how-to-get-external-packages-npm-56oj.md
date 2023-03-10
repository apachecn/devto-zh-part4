# NodeJS:如何获得外部包/ npm

> 原文：<https://dev.to/miku86/nodejs-how-to-get-external-packages-npm-56oj>

## [T1】简介](#intro)

所以我们[在我们的机器上安装了 NodeJS](https://dev.to/miku86/what-is-nodejs-how-to-install-it-1900) 。

因为互联网上已经有很多代码了，我们可以使用其他人的代码，这样我们就不必重新发明轮子来解决已经解决的问题。

为了容易地从其他人那里得到解决方案，我们可以使用一个叫做 npm 的工具。

这将是 npm 的一个简短介绍，包含最常用的命令。如果你想学习一些高级概念，请阅读 [npm 文档](https://docs.npmjs.com/)。

* * *

## 什么是 npm？

*   npm 是 JavaScript 包的包管理器
*   npm 是世界上最大的软件注册中心
*   npm 附带了 NodeJS 安装程序，所以如果您已经[安装了 NodeJS](https://dev.to/miku86/what-is-nodejs-how-to-install-it-1900) ，那么您的机器上已经有 npm 了
*   您可以使用命令行界面下载软件包
*   你可以通过 [npm 网站](https://www.npmjs.com/)获得关于包裹的信息

* * *

## 如何安装 npm？

如果您已经[安装了 NodeJS](https://dev.to/miku86/what-is-nodejs-how-to-install-it-1900) ，那么您的机器上已经有了 npm。

检查是否安装了 NPM:
`npm --version`

* * *

## 如何获取外部包？

大多数时候，您希望在项目层面使用包。

1.  在现有文件夹中初始化 npm 项目:`npm init`。这将创建一个`package.json`文件，保存您的所有设置。
2.  回答所有类似`package name`、`version`等问题。你可以在这里阅读[的正确答案](https://docs.npmjs.com/files/package.json)
3.  进入 [npm](https://www.npmjs.com) ，搜索一个包，例如[“文字颜色”](https://www.npmjs.com/search?q=text%20color)。
4.  您将获得匹配包的列表，单击包的链接并阅读文档，例如包[“chalk”](https://www.npmjs.com/package/chalk)。
5.  大多数时候，文档会给你一个合适的[命令来安装包](https://www.npmjs.com/package/chalk#install)，比如`npm install chalk`。如果你想知道如何`npm install`一个包的所有可能性，请阅读[NPM 安装文档](https://docs.npmjs.com/cli/install)。
6.  包被下载，你必须等待一段时间。
7.  阅读软件包文档以了解如何使用软件包。

* * *

## 附加命令

如果你想学习额外的命令，例如卸载一个软件包，[阅读文档](https://docs.npmjs.com/cli-documentation/)

* * *

## 进一步阅读

[npm 主页](https://docs.npmjs.com/about-npm/)
[npm 安装文档](https://docs.npmjs.com/cli/install.html)
[npm 上的维基百科](https://en.wikipedia.org/wiki/Npm_(software))

* * *

## 提问

*   你希望在这里也能解决哪些初学者的问题？
*   说明是否清晰、易懂、易懂？