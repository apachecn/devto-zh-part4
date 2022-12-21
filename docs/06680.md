# 与 Pugjs 共享 HTML 布局

> 原文：<https://dev.to/nkratzmeyer/shared-html-layouts-with-pugjs-2j04>

本文是关于使用 PugJS 模板引擎和 nodejs 和 expressjs 的系列文章的第二篇。在[第一部分](https://dev.to/nkratzmeyer/html-templating-with-pugjs-7m9)中，我回顾了使用 Pugjs 呈现 HTML 页面的基础知识。在这篇文章中，我将演示如何使用 Pug 来创建和呈现共享布局模板。我们开始吧！

## 起始代码

下面是上一篇文章末尾的代码。

```
//   index.js
const express = require('express');

const app = express();

app.set('view engine', 'pug');

app.get('/', (req, res)=>{
    res.render('index');
});

app.listen(3000, ()=>{
    console.log('Server listening on 3000');
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
//- Index.pug
<!DOCTYPE html>
html(lang="en")
    head
        title Home Page
    body
        header
            h1 Hello from Pug Template!
        main
            p This is a paragraph 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的文件夹布局

```
project_folder
>node_modules
--Lots of stuff
>views
--index.pug
-index.js
-package-lock.json
-package.json 
```

Enter fullscreen mode Exit fullscreen mode

## 添加一个布局

在**视图**目录下，新建一个名为**布局**的子目录。在这个目录下，创建一个名为 **main-layout.pug** 的新文件，内容如下。

```
<!DOCTYPE html>
html(lang="en")
    head
        title Home Page
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
```

Enter fullscreen mode Exit fullscreen mode

所以这只是一个普通的 pug 模板，除了最后一行的**块**元素。这表示使用这种布局的任何其他 Pug 模板将提供的内容块。**块**标识符旁边的单词“内容”只是一个名称，可以是任何东西。所以`block content`只是为其他 Pug 页面注入的内容创建一个命名的占位符。稍后我们将看到如何创建多个这样的占位符块。现在让我们修改我们的 **index.pug** 文件来使用我们的布局。打开 **index.pug** ，将其内容改为:

```
extends layouts/main-layout.pug

block content
    p This content is from index.pug! 
```

Enter fullscreen mode Exit fullscreen mode

第一行告诉 Pug 引擎使用我们刚刚创建的 main-layout.pug 文件。第二行写着“在渲染之前，将以下内容插入到**主布局**文件的名为 **content** 的块中”。因此，您应该能够启动 nodemon 并查看位于 [http://localhost:3000](http://localhost:3000) 的页面。

## 添加关于页面

在我们的 **main-layout** 文件中，我们创建了一个到 **/about** 的链接，但是我们还没有在我们的服务器上创建这个页面或者这个 route handler。我们现在就开始吧。打开 **index.js** ，在我们的“/”处理程序下添加下面的路由处理程序。

```
app.get('/about', (req, res)=>{
    res.render('about');
}); 
```

Enter fullscreen mode Exit fullscreen mode

在我们的**视图**文件夹下，让我们创建一个包含以下内容的 **about.pug** 文件。

```
extends layouts/main-layout.pug

block content
    p This content is from about.pug!
    p About my cool site! 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有两个页面使用了**主布局**文件，每个页面都有不同的内容。如果你回到网站，你的导航链接现在应该工作。

## 多个命名块

现在让我们看看如何在布局页面中创建多个占位符块。打开 **main-layout.pug** 并将输入以下代码放在我们现有的代码下。

```
section
    h2 This h2 is also from the layout
block more_content 
```

Enter fullscreen mode Exit fullscreen mode

确保**部分的**标签缩进到与`block content`行相同的水平。修改 **index.pug** 以使用新的占位符。在 **index.pug** 中我们现有的代码下添加以下内容。

```
block more_content
    p More content from index.pug!! 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您在 home route 上查看该站点，您应该会看到页面的不同部分是由不同的文件提供的。注意，在我们的 **about.pug** 文件中，我们没有为 **more-content** 块提供任何东西。如果你导航到**关于**的页面，你会发现这不是问题。

## 结论

在这篇文章中，我介绍了如何使用 Pugjs 来呈现共享的 HTML 布局。我们已经了解了如何在布局中表示占位符块，以便可以从使用该布局的其他页面中注入内容。在本系列的下一篇(也可能是最后一篇)文章中，我将介绍如何从服务器向 Pug 模板注入动态数据。希望这有所帮助，欢迎指正/评论/批评！