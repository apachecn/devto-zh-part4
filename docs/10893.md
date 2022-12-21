# 获得个性化引导的五个步骤

> 原文：<https://dev.to/lukaszsarzynski/five-steps-to-get-personalized-bootstrap-56f6>

## ..使用 Gulp 4 和 SASS 预处理程序

> [🚅Bootstrap](https://getbootstrap.com/) 很神奇，让前端 web 开发变得更快更容易。但是怎么改变里面的东西呢？如果你需要一个**不同的原色**或者规则的小装置**576 px 开始不适合你**。你随时可以改变它。所以，让我们开始吧！🎢

### #0 一饮而尽

通过 *npm* 包直接安装吞咽工具将是一种快捷的方式。

> 如果你还没有 npm，让我们只安装 [NodeJs](https://nodejs.org) 这个`Node Package Manager`会在里面。

```
npm install gulp-cli --global 
```

Enter fullscreen mode Exit fullscreen mode

### #1 下载 Bootstrap、Gulp 和 SASS 工具

保存最新的引导版本，将 SASS 预处理工具加入到你的项目中。

```
npm install gulp gulp-sass bootstrap --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

### #2 复制引导程序到本地版本

事实是 npm 在`./node_modules/`中创建了很多目录。看，在`./node_modules/bootstrap/scss/`中，我们有引导 CSS 框架的源代码，这是

```
so lets copy all of './node_modules/bootstrap/scss/*' directory to './bootstrap-source/' localy 
```

Enter fullscreen mode Exit fullscreen mode

### #3 更改引导程序，将其个性化

你可以做你想做的，但是我有自己的计划。

> 我必须将小型设备的断点规则从 576px 改为 370px】

```
/* @file: ./bootstrap-source/_variables.scss */

// Grid breakpoints
// Define the minimum dimensions at which your layout will change,
// adapting to different screen sizes, for use in media queries.
$grid-breakpoints: (
  xs: 0,
  sm: 370px, // @note: this is exactly what I need (before was 576px there)
  md: 768px,
  lg: 992px,
  xl: 1200px
) !default; 
```

Enter fullscreen mode Exit fullscreen mode

### #4 告诉大口萨斯需要什么

创建`./gulpfile.js`并将源目录设置为`./bootstrap-source/`，将目的目录设置为`./css/`T3

```
/* @file: ./gulpfile.js */

const { src, dest } = require('gulp');
const sass = require('gulp-sass'); // Gulp SASS plugin

function compileBootstrap() {
  return src('./bootstrap-source/**/*.scss') // Input
    .pipe(sass().on('error', sass.logError))
    .pipe(dest('./css')); // Output

}

exports.default = compileBootstrap; 
```

Enter fullscreen mode Exit fullscreen mode

### #5 享受自己的自举

运行大口

```
gulp 
```

Enter fullscreen mode Exit fullscreen mode

这就是全部。准备好你修改的 CSS 引导程序，你可以在`./css/`文件夹中找到。

* * *

### #Inf 摘要

在这个修改之后，我自己的 Bootstrap 有了不同的表现，例如类:*。d-****sm****-block，。浮动-****sm****-右，。正文-****sm****-对，。先生-****sm****——汽车*及其他来自二百****——sm-****的例子。

> scss 文件中的一个变化会产生很大的影响。我害怕你会用它做什么。**所以，就这么做**。获得自己的自举版本，在你的具体项目好看！🌟

* * ***