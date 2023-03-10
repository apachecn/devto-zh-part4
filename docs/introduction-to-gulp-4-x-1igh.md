# Gulp 4.x 简介

> 原文：<https://dev.to/mporam/introduction-to-gulp-4-x-1igh>

有一篇关于 CSS 技巧的非常棒的文章叫做 [Gulp for 初学者](https://css-tricks.com/gulp-for-beginners/)，它比我更好地涵盖了这个主题，然而，不幸的是，它与 Gulp 3.x 有关，随着 Gulp 4.x 的发布，它发生了不小的变化

在 [iO Academy](https://io-academy.uk) 我们曾经使用 CSS Tricks 文章作为参考，这篇文章试图用 Gulp 4.x 的更新示例重新创建 CSS Tricks 文章中的教程要点。如果你想了解什么是 Gulp 以及我们为什么使用它，我强烈建议你使用前面提到的文章。

# 安装大口

假设您已经安装了[节点和 NPM](https://nodejs.org/)，我们需要从安装 Gulp 到我们的项目开始，为此，从您的项目文件夹
的根目录运行以下命令

```
$ npm install gulp --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:这将 Gulp 作为一个 dev 依赖项安装到您当前的项目中，确保您已经创建了一个新的项目文件夹，并且在运行该命令之前已经导航到该文件夹。

如果您在命令执行完毕时检查项目文件夹，您应该看到`npm`已经创建了一个`node_modules`文件夹。您还应该在`node_modules`中看到一个`gulp`文件夹。

现在我们已经安装了 Gulp，我们可以开始使用它了，我们将假设下面的目录结构，如果你正在使用一个不同的目录，你可能需要调整你的 gulp JS 来适应它。

```
 |- app/
      |- css/
      |- index.html
      |- js/ 
      |- scss/
  |- gulpfile.js
  |- node_modules/
  |- package.json 
```

Enter fullscreen mode Exit fullscreen mode

# 创建您的第一个吞咽任务

首先，我们需要要求吞咽到你的 gulpfile.js

```
var gulp = require('gulp'); 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以开始用这个 gulp 变量编写一个 gulp 任务。吞咽任务的基本语法是:

```
function taskName(cb) {
  // code for your task here
  cb();
}

exports.taskName = taskName; 
```

Enter fullscreen mode Exit fullscreen mode

将上述示例中出现的所有`taskName`替换为运行 gulp 时希望使用的命令名称，即`gulp taskName`

这方面的一个例子应该是这样的。

```
function hello(cb) {
  console.log('hello');
  cb();
}

exports.hello = hello; 
```

Enter fullscreen mode Exit fullscreen mode

然后您可以使用`gulp hello`从命令行运行它。

# 与大口斗嘴

既然我们已经设置了 Gulp，我们将使用它来编译我们的 Sass。首先，我们需要安装 gulp-sass 模块作为开发依赖项。

```
$ npm install gulp-sass --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，我们需要将它放入 gulpfile.js

```
var gulp = require('gulp');
// Requires the gulp-sass plugin
var sass = require('gulp-sass'); 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要创建我们的 sass 任务

```
function sassCompile(cb) {
  return gulp.src('app/scss/style.scss')
    .pipe(sass()) // Converts Sass to CSS with gulp-sass
    .pipe(gulp.dest('app/css'));
  cb();
}
exports.sass = sassCompile; 
```

Enter fullscreen mode Exit fullscreen mode

一旦任务设置好了，我们可以通过在`app/scss`文件夹中创建一个`style.scss`文件并添加一些 Sass 来测试它是否工作。下面是一些示例代码。

```
$blue: #0000ff;

body {
  background: $blue;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您在命令行中运行`gulp sass`，您应该看到在`app/css`中创建了一个`styles.css`文件，并被编译成 css。

上面的例子将一个文件编译成一个文件，虽然有不同的 css 架构方法，但我通常建议在一个基础文件(如 style.css)中使用 Sass 导入，然后导入所有单独的 CSS 文件。如果您使用不同的模式，但是您可能希望编译多个文件，那么您可以使用通配符选择器或节点 globs 来实现这一点，方法是将 src 替换为如下内容。这将编译所有。`app/scss`内的 scss 文件以及该文件夹的所有子目录。每个 scss 文件都将成为等效的 css 文件。

```
app/scss/**/*.scss 
```

Enter fullscreen mode Exit fullscreen mode

# 自动编译 scss 文件

虽然能够用一个简单的命令编译我们的 scss 文件是很好的，但这并不特别实用，相反，我们希望创建一个观察器，它将在检测到变化时自动编译我们的 sass。

我们可以通过设置一个吞咽监视器来做到这一点。使用与 gulp 任务相似的语法，我们首先创建一个命名函数并调用`gulp.watch`。这需要两个参数，要监视的文件模式(在这个例子中是所有的`.scss`文件)和当这些文件中的任何一个发生变化时要运行的函数。

```
function watch() {
  gulp.watch('app/scss/**/*.scss', sassCompile);
}

exports.watch = watch; // dont forget to export your command! 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:要查看的文件是*所有的* `.scss`文件，而不仅仅是主`style.scss`文件，否则任何文件发生变化都不会自动编译。`sassCompile`函数中的文件应该仍然只指向`style.scss`文件，这样当监视器被触发时，它只编译那个文件(以及它包含的所有内容)。

您可以对您可能需要的任何其他观察器使用这个观察器功能，因此您可以用一个命令启动所有的观察器。

```
$ gulp watch 
```

Enter fullscreen mode Exit fullscreen mode

你还可以用 Gulp 做很多其他有用的事情，下面列出了一些你可能想了解的事情。

*   [自动修复程序](https://www.npmjs.com/package/gulp-autoprefixer) -独立于供应商的 CSS
*   [Sourcemaps](https://www.npmjs.com/package/gulp-sourcemaps) -编译后的 CSS 和 JS 调试更容易