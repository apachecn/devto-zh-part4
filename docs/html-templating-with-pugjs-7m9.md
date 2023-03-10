# 使用 Pugjs 的 HTML 模板

> 原文：<https://dev.to/nkratzmeyer/html-templating-with-pugjs-7m9>

今天我学习了使用 Pugjs 渲染 HTML 模板和布局。我愿意分享我所学到的东西，以帮助其他人开始使用帕格，同时巩固我自己的理解。本教程假设您熟悉 Nodejs 和 Express。所以事不宜迟，我们开始吧。

### 设置项目

导航到您选择的项目目录并运行以下命令，使用所有默认值初始化 NPM:
`npm init -y`

接下来运行以下命令来安装 Express 和 Pug:
`npm i express pug`

同样使用下面的命令将 nodemon 安装为 dev 依赖项:
`npm i nodemon --save-dev`

现在，在该目录中打开您最喜欢的代码编辑器，并将以下内容添加到 package.json 文件的脚本部分。
`"dev" : "nodemon index.js"`

现在用下面的代码创建一个 index.js 文件。

```
const express = require('express');
const app = express();

app.get('/', (req, res)=>{ 
    res.send('Testing 123');
});

app.listen(3000, ()=>{
    console.log('Server listening on 3000');
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在用下面的命令从终端启动应用程序:
`npm run dev`

现在，您应该能够在浏览器中点击 [http://localhost:3000](http://localhost:3000) 并看到测试页面。

## 添加 Pugjs 页面

在您的项目文件夹中，添加一个名为 **views** 的新子文件夹。按照惯例，当您请求呈现一个视图时，Pug 会查看这里。你可以叫这个文件夹别的名字，但是你必须告诉帕格去哪里找。为此，请参考 Pug 文档。
在 views 文件夹中，创建一个名为 **index.pug** 的新文件，内容如下。

```
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

上面的代码演示了 Pug 模板的基本语法。注意，它基本上是去掉了标签标记<>的 HTML。每个子元素都比其父元素多缩进 1 个制表符。还要注意没有结束标签。
现在让我们告诉 Express 使用 Pug 作为它的视图引擎。我们可以通过在应用程序声明后立即将下面的代码插入到我们的 **index.js** 文件中来实现。

```
const app = express();

// New code
app.set('view engine', 'pug'); 
```

Enter fullscreen mode Exit fullscreen mode

上面的`app.set(..`语句将 Pugjs 设置为 Express 的视图引擎。注意，在此之前我们不必导入 pugjs。现在，当浏览器请求回家路线时，我们可以呈现 index.pug 页面。在 **index.js** 中，替换此行:

```
res.send('Testing 123'); 
```

Enter fullscreen mode Exit fullscreen mode

有了这个:

```
res.render('index'); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们不必在 render 语句中指定 **'index.pug'** 。现在在你的浏览器中点击 [http://localhost:3000](http://localhost:3000) ，看看我们漂亮的页面。

## 结论

在这篇文章中，我讲述了使用 Pugjs 渲染 HTML 页面的基础知识。这本身并不特别令人印象深刻，但请继续关注！在我的下一篇文章中，我将回顾 Pugjs 布局的使用，这样我们就可以模块化 HTML 的各个部分(非常确定是一个单词)以避免重复代码。希望这是有帮助的，欢迎评论/批评！