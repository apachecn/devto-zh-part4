# 在 PugJS 中使用包含/部分

> 原文：<https://dev.to/nkratzmeyer/using-includes-partials-in-pugjs-19fa>

本文是将 PugJS 视图引擎与 NodeJS 和 ExpressJS 结合使用的系列文章的第四篇。在[的最后一部分](https://dev.to/nkratzmeyer/displaying-dynamic-data-with-pugjs-18km)中，我们介绍了使用 PugJS 来显示动态数据。在本帖中，我们将介绍使用**包括**(在 PugJS 文档中命名)的概念。我更愿意称它们为“部分”,在本文中也会这样做。我们开始吧！

## 起始代码

下面是我们的起始代码(自上一篇文章以来略有修改)。

```
//index.js
const express = require('express');

const userNames = ['Sean', 'George', 'Roger', 'Timothy', 'Pierce', 'Daniel']
const bestMovie = 'Casino Royale';
const licensedToKill = true;

const app = express();

app.set('view engine', 'pug');

app.get('/', (req, res) => {
    res.render('index', {
        userNames,
        bestMovie,
        licensedToKill,
        title : "Home Page"
    });
});

app.get('/about', (req, res) => {
    res.render('about', {
        userNames,
        title : 'About'
    });
});

app.listen(3000, () => {
    console.log('Server listening on 3000');
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
//- main-layout.pug
<!DOCTYPE html>
html(lang="en")
    head
        title #{ title }
    body
        header
            nav
                ul
                    li 
                        a(href="/") Home
                    li
                        a(href="/about") About
        h1 This h1 is from the layout.
        hr
        block content
        section
            h2 This h2 is also from the layout
            for user in userNames
                li= user
        block more_content 
```

Enter fullscreen mode Exit fullscreen mode

```
//- Index.pug
extends layouts/main-layout.pug
block content
    if licensedToKill
        p Bond is licensed to kill!
    else
        p Bond is NOT licensed to kill!
block more_content
    p The best movie is #{ bestMovie }
    p= bestMovie 
```

Enter fullscreen mode Exit fullscreen mode

```
//- about.pug
extends layouts/main-layout.pug
block content
    p This content is from about.pug!
    p About my cool site! 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的文件夹布局

```
project_folder
├── index.js
├── node_modules
├── package.json
├── package-lock.json
└── views
    ├── about.pug
    ├── index.pug
    ├── layouts
    │   └── main-layout.pug 
```

Enter fullscreen mode Exit fullscreen mode

## 关于偏旁(包括)

includes 背后的概念非常简单。它为我们提供了一种将一个文件的内容拉入另一个文件的机制。为此，我们使用 **include** 关键字，后跟部分文件的路径。这有点类似于本系列前几篇文章中讨论的“布局”概念。我认为模板系统是为你的网站创建一个总体布局的好方法，而分部提供了一个创建可重用的模块化组件的好方法。让我们看看如何将片段集成到现有代码中。

## 使用分部分项为表头

我们的**主布局**文件中的 header 元素看起来像是 partial 的一个很好的候选。在真实的网站中，标题和导航元素可能会有点复杂，最好将标题分离到不同的文件中，以便于阅读和维护。首先，在**视图**下创建一个名为**片段**的新文件夹。然后在 **partials** 文件夹中创建一个名为 **header.pug** 的新文件。现在从主布局中剪切标题元素并粘贴到 **header.pug** 中。我们还将添加一个带有一些文本的 h1。它应该是这样的:

```
//- header.pug
header
    nav
        ul
            li 
                a(href="/") Home
            li
                a(href="/about") About
    h1 This page title is coming from header.pug -- #{ title } 
```

Enter fullscreen mode Exit fullscreen mode

在 **main-layout.pug** 中，通过在标题之前的位置添加这一行来包含标题。一定要适当缩进。它应该比我们的**主体**元素:
多缩进一个制表符

```
 include ../partials/header 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您查看您的页面，您应该会看到我们有相同的最终结果。如果您在浏览器中查看页面源代码，您应该看到我们有格式良好的 HTML。如果你没有格式良好的 html，那么你的 pug 语法就有错误。在我们虚构的例子中，这样做并没有太多好处。然而，如前所述，这在更复杂的网站中非常有用。让我们做一些稍微有用的事情。

## 创建用户卡

现在我们将创建一个可重用的组件来显示用户。在 **partials** 中创建一个名为 **user.pug** 的新文件，内容为:

```
div.card
    p= user 
```

Enter fullscreen mode Exit fullscreen mode

当这被翻译成 html 时，我们将得到一个带有**卡**类的 **div** 。在 main-layout.pug 中，将用户列表修改为:

```
for user in userNames
    include ../partials/user.pug 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们想在我们站点的任何地方显示一个用户，我们可以只包含我们的 **user.pug** 确保它可以访问一个 **user** 对象。
作为进一步的练习，你可以为一个页脚或另一个导航元素创建一个 partial。**重要提示:**在这些例子中，我只在 **main-layout** 文件中使用了部分。然而，这不是必需的。我们可以在任何 pug 文件中使用 **include** 关键字。

## 避此！

你可能会认为我们可以将 index.pug 的第一行改为:

```
include partials/main-layout.pug 
```

Enter fullscreen mode Exit fullscreen mode

然而，这是行不通的！html 和 **body** 标签会过早关闭，我们的 html 会被顶起！

## 结论

在这篇文章中，我复习了 PugJS 中的 **includes/partials** 。我们已经看到了如何将视图分割成单独的文件，以帮助重用和可读性。希望这篇文章信息丰富，一如既往，欢迎指正/评论/批评！