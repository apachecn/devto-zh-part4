# 便签

> 原文：<https://dev.to/dialaeke/handy-note-96l>

大家好。这是我第一次在任何地方发表与编程相关的文章。

在大学学习机械工程的最后一年，我对编码产生了兴趣，因为:

我讨厌机械工程。我入行是因为我想成为我父亲那样的人。

b)。我的成绩很差。

我决定进入编码领域，剩下的就是历史了。

最近，我发现自己随身带着一张小纸条，因为我的大部分阅读都是在电脑上用浏览器完成的。有时，我会忘记带着笔记去什么地方，我不得不买一本新的。我积累了相当多的笔记，这有点违背了做笔记的目的，因为我可以在不同的笔记中找到与一个主题相关的笔记，而在我需要的时候却找不到。

我安装了相当多的扩展，但他们大多打开了一个新标签。我只是想弹出一个输入窗口，然后下载我输入的内容。

上周我决定自己制作它，今天它以“Handy notes”的名字出现在 chrome 网上商店。

我使用了 klocal 的 vue web 扩展库

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [科卡尔](https://github.com/Kocal) / [ vue-web-extension](https://github.com/Kocal/vue-web-extension)

### 🛠️是用 Vue、webpack 4、ESLint 等快速启动 web 扩展的样板文件！

<article class="markdown-body entry-content p-5" itemprop="text">

# 视图-web 扩展

[![Build Status (Travis)](img/7befd8ea3e97522643b3fa5936ac1e97.png) ](https://travis-ci.org/Kocal/vue-web-extension) [ ![Build Status (AppVeyor)](img/747e89367f1e074cc5600e7861e8a5a6.png)](https://ci.appveyor.com/project/Kocal/vue-web-extension/branch/master)

此模板允许您快速启动包含以下内容的 web 扩展:

*   用于`manifest.json`和`background.js`文件以及`icons` / `popup`文件夹的样板文件
*   视图
*   [Vue 路由器](https://github.com/vuejs/vue-router)(可配置)
*   [Vuex](https://github.com/vuejs/vuex) (可配置)
*   [轴](https://github.com/axios/axios)(可配置)
*   [网络包 4](https://github.com/webpack/webpack)
*   [巴别塔](https://github.com/babel/babel)与[预置-环境](https://github.com/babel/babel/tree/master/packages/babel-preset-env)
*   [ESLint](https://github.com/eslint/eslint) (可配置)
*   [更漂亮](https://github.com/prettier/prettier)(可配置)
*   一个 git 预提交钩子，通过使用 [pretty-quick](https://github.com/azz/pretty-quick) 或 [precise-commits](https://github.com/nrwl/precise-commits) (可配置)来运行得更漂亮
*   CSS 提取，带有[迷你 css-extract-plugin](https://github.com/webpack-contrib/mini-css-extract-plugin)
*   符合 Chrome 和 Firefox 网络商店的 **C** 内容 **S** 安全 **P** 策略(删除了一些用法 [`eval`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/eval)
*   将您的扩展打包成一个`.zip`文件的脚本

## 要求

*   Node.js >= 8，npm >= 5
*   [去](https://git-scm.com)
*   vista-CLI 2

## 使用

```
$ vue init kocal/vue-web-extension my-extension
$ cd my-extension
$ npm install
$ npm run build
```

### `npm run build`

将扩展构建到**生产**的`dist`文件夹中。

### `npm run build:dev`

将扩展构建到**开发**的`dist`文件夹中。

### `npm run watch`

观察修改，然后运行`npm run build`。

…</article>

[View on GitHub](https://github.com/Kocal/vue-web-extension)to scaffold it.

我已经在 chrome 网上商店免费提供了它，代码也可以在 github 上找到

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [埃克迪亚拉](https://github.com/ekediala) / [ web-note-extension](https://github.com/ekediala/web-note-extension)

### 网上冲浪时记笔记。

<article class="markdown-body entry-content p-5" itemprop="text">

# 网络笔记扩展

网上冲浪时记笔记。

</article>

[View on GitHub](https://github.com/ekediala/web-note-extension)

.

我希望能写一篇关于我是如何为像我这样的新手做扩展的文章并发表出来。

我欢迎评论和批评。谢谢你耐心阅读我的胡言乱语。

下面是使用中的扩展。

[![handy notes image](img/23e03b166e0bea209a3192d008242c51.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oCR9q6n---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wjpb9du0dlt76qkkucck.jpeg)