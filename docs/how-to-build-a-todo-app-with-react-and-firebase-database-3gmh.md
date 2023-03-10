# 如何用 React 和 Firebase 数据库构建 todo 应用程序

> 原文：<https://dev.to/rossanodan/how-to-build-a-todo-app-with-react-and-firebase-database-3gmh>

## 第二天——走向无限及更远

📅2019 年 6 月 28 日
🕐2h
🏁模仿应用程序状态，实现一些动作处理程序并重新设计应用程序

我已经很久没有发表文章了。我一直很忙(当然不只是为了看《玩具总动员 4》的循环版)。

无论如何，我邀请你在 GDG·戈尔韦论坛上继续阅读，在那里我发表了我的第一个 DevTalk 的所有代码。

我向我们在戈尔韦的追随者介绍了 React，我们在意大利的一些朋友通过 Google Hangouts 关注了我们。这是一次很棒的经历，我们正在安排未来的约会。

**别担心**，我会实现 Firebase 来保存一些数据。目前，嘲弄已经足够了。但是如果你不想等..嘿伙计！自己做！*最好的学习方法是“边做边学”，你不知道吗？*

* * *

在论坛上，你可以找到另一个不错的代码实验室，作者是我在 GDG 高威的同事朱利安。他介绍了 NodeJS。

React 教程[https://github.com/gdg-galway/codelab-react-introduction](https://github.com/gdg-galway/codelab-react-introduction)GDG 高威官方论坛[https://forum.gdg-galway.com/](https://forum.gdg-galway.com/)

点击此处查看代码

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[gdg-galway](https://github.com/gdg-galway)/[code lab-react-简介](https://github.com/gdg-galway/codelab-react-introduction)

### 在 GDG 高威 React Codelab 用 React 构建的简单的单页应用程序。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 欢迎来到 React Codelab <g-emoji class="g-emoji" alias="wave" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f44b.png">👋</g-emoji>

通过这个代码实验室，我们将使用 React 开发一个简单的待办应用程序，学习这个库的`basic concepts`和一些`ES6 features`。

## 初始设置

首先，确保您的机器上安装了 **NodeJS** 和 **npm** ，打开您的终端并运行`node --version`和`npm --version`:如果一切正常，您应该会看到类似这样的内容

```
rossanos-mbp-2:codelab-react-introduction rossanodangelo$ node --version
v11.14.0
rossanos-mbp-2:codelab-react-introduction rossanodangelo$ npm --version
6.9.0
```

Enter fullscreen mode Exit fullscreen mode

为了轻松搭建这个项目，我将使用脸书提供的工具`create-react-app`。

```
npx create-react-app codelab-react-introduction
cd codelab-react-introduction
npm start
```

Enter fullscreen mode Exit fullscreen mode

`npm start`命令会将您的启动项目运行到`http://localhost:3000/`。

## 盒子里有什么

使用您最喜欢的文本编辑器(我推荐使用 **Visual Studio 代码**)，打开项目以查看默认结构:

```
./node_modules
./public
./src
App.js
App.test.js
App.css
.gitignore
package-lock.json
package.json
README.md 
```

在这个代码实验室中，我们将…

</article>

[View on GitHub](https://github.com/gdg-galway/codelab-react-introduction)