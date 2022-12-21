# 如何快速生成快递 App 骨架然后编辑

> 原文：<https://dev.to/yogesnsamy/how-to-quickly-generate-an-express-app-skeleton-and-then-edit-it-5b00>

*   首先，您必须安装快速生成器，键入:
    **NPM install-g express-generator**

*   接下来运行下面的命令，用 *ejs* 引擎创建一个 express 应用程序:
    **express-view = ejs kiku care**
    *kiku care*是我们正在创建的应用程序的名称。

[![Alt Text](img/c67f2dc548d3d75ee287da80683c8190.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ybhYJdbt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eo91x5c0t6krkz076p49.jpg)

*   进入新创建的应用文件夹，输入:
    **cd kikucare/**

*   键入以下命令安装所有依赖项:
    **npm 安装**

*   接下来通过输入命令启动应用程序:
    **DEBUG = kiku care:* NPM start**

*   你现在可以通过进入地址**[http://localhost:3000/](http://localhost:3000/)**在浏览器上查看应用。

*   你会看到如下:
    [![Alt Text](img/70f10b450a7849bd5f37103b2348a175.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1kH6_ePM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9640t51q387njffz5j0u.jpg)

#### 让我们对浏览器上显示的内容做一个简单的更改！

*   我们将从文件夹 **routes** 中编辑文件 **index.js** 。
    [![Alt Text](img/3e83493be3decd5cdf78e623c3437604.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xgVTkNPj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6nag10xijr3uemtd0a42.jpg)

*   修改第 6 行，将*标题*从‘Express’替换为‘kiku care’
    [![Alt Text](img/e26a6a61e0bab364d9a13194f202d5ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o1LYGH02--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/imzh5388pp88gqbyczhy.jpg)

*   输出:
    [![Alt Text](img/46ec304f041a80287bfcc2387d79a801.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sm-JOupV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/okgk1paf67xxnr1wqbk0.jpg)