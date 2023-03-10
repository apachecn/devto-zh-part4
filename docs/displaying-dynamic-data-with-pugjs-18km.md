# 用 PugJS 显示动态数据

> 原文：<https://dev.to/nkratzmeyer/displaying-dynamic-data-with-pugjs-18km>

本文是将 PugJS 视图引擎与 NodeJS 和 ExpressJS 结合使用的系列文章的第三篇。在[的最后一部分](https://dev.to/nkratzmeyer/shared-html-layouts-with-pugjs-2j04)中，我们介绍了使用 PugJS 创建共享的 HTML 布局。在本帖中，我们将介绍如何将数据从 express 服务器传递到 Pug 页面。我们开始吧！

## 起始代码

下面是上一篇文章末尾的代码。

```
//   index.js
const express = require("express");

const app = express();

app.set("view engine", "pug");

app.get("/", (req, res) => {
  res.render("index");
});

app.get("/about", (req, res) => {
  res.render("about");
});

app.listen(3000, () => {
  console.log("Server listening on 3000");
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
//- main-layout.pug
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
        section
            h2 This h2 is also from the layout
        block more_content 
```

Enter fullscreen mode Exit fullscreen mode

```
//- Index.pug
extends layouts/main-layout.pug
block content
    p This content is from index.pug!
block more_content
    p More content from index.pug!! 
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
>node_modules
--Lots of stuff
>views
>>layouts
---main-layout.pug
--about.pug
--index.pug
-index.js
-package-lock.json
-package.json 
```

Enter fullscreen mode Exit fullscreen mode

## 添加一些数据

让我们把一些虚拟数据放入我们的服务器，这样我们就有东西可以传入我们的 Pug 页面。打开 **index.js** ，在我们的 **app** 声明之前插入以下内容。

```
const userNames = ["Sean", "George", "Roger", "Timothy", "Pierce", "Daniel"];
const bestMovie = "Casino Royale";
const licensedToKill = true; 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们将这些数据传递到我们呈现的页面中。在 home route 处理器上，将 **res.render** 代码修改如下。

```
res.render("index", {
  userNames,
  bestMovie,
  licensedToKill
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们将一个对象传递给视图引擎。这种向引擎传递数据的方法似乎是主流视图引擎的标准。正如我们接下来将看到的，传递的对象的属性将在 Pug 模板中可用。

# 显示数据

让我们从在主页上显示**最佳电影**开始。打开 **index.pug** 并修改 **more_content** 块以匹配下面的代码:

```
block more_content
    p The best movie is #{ bestMovie }
    p= bestMovie 
```

Enter fullscreen mode Exit fullscreen mode

现在检查回家的路线，看看结果。第一个段落元素显示了我们如何在内容中执行插值。第二段演示了如何在没有插值的情况下设置元素的文本内容。

## 条件逻辑

现在让我们看看在我们的 Pug 页面中集成一点条件逻辑。在 **index.pug** 中，修改**内容**块以匹配以下内容:

```
block content
    if licensedToKill
        p Bond is licensed to kill!
    else
        p Bond is NOT licensed to kill! 
```

Enter fullscreen mode Exit fullscreen mode

尝试切换 **index.js** 中 **licensedToKill** 的值，看看它是如何改变页面呈现的。Pug 为我们提供了一种基于传入数据执行条件渲染的简洁方法。

## 循环

现在让我们看看如何迭代我们的用户名数组。这次让我们在 **main-layout.pug** 中工作，只是为了展示我们可以在布局文件中使用动态数据。在我们现有的 **h2** 下，增加以下内容( **ul** 应缩进到与 **h2** 相同的级别):

```
ul
    for user in userNames
        li= user 
```

Enter fullscreen mode Exit fullscreen mode

同样，循环数组的语法非常简洁。
**注意:**此时，如果你转到**关于**的页面，你会在页面上收到一个错误，因为我们还没有传入该路线的任何数据，但是 **main-layout.pug** 页面正在寻找一个**用户名**数组。作为练习，修改路由处理程序以传入必要的数据。

## 结论

在这篇文章中，我介绍了如何使用 PugJS 来呈现从服务器接收的数据。我们已经看到了如何显示简单的数据，如何使用插值、条件逻辑以及用 PugJS 进行循环。希望这篇文章和系列文章对任何将 PugJS 视为视图引擎的人都是有用的和有帮助的。一如既往，欢迎指正/评论/批评！