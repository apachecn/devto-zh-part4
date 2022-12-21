# 在我们的网站上使用 GulpJs

> 原文：<https://dev.to/uatthaphon/gulpjs-1kbk>

我将创建一个新的网站，有 [Hugo](https://gohugo.io/)
，通过使用 [GulpJs](https://gulpjs.com/) 帮助组织。

> 如果有人想学习用 Hugo 创建网站，我建议在[Hugo+firebase } t1 上浏览我以前的文章。](https://atthaphon.urairat.me/2017/12/05/hugo---firebase/)

## 在本文中需要什么

我使用 Mac 来工作，所以我们将主要使用 Command line 来管理
安装工具，包括通过 windows 创建一个 project builder。

## [t1㎡形成一个旋塞](#%E0%B8%AA%E0%B8%A3%E0%B9%89%E0%B8%B2%E0%B8%87-gulp-pipeline)

本文将讨论安装和使用 Gulp 来管理将文件 SCSS 转换为 CSS \ t0 }文件，以便在该图像的一部分中与网站一起使用。jpg，或者 png，把它变成一个文件。jpg
并通过编译将 JS 文件降级为 CSS，最后在编译
时加入版本号为 finder。

我们将使用 Gulp 与 Hugo，因此我们必须将我们的所有已编译好的文件放在`static/`【文件夹】T2 }上。

从创建一个文件夹开始，用于存储我们将要在这个项目中运行的文件。
包含文件夹“`scss`的“T3”。

```
$ mkdir -p src/{scss,js,images} 
```

### 安装 Gulp

我们将使用`--yes` flag 进行快速安装。

```
$ npm init --yes 
```

> `--yes`标志将自动为我们的 npm 项目创建基本值。

### 所需的软件包

下面是我们将在本文中使用的 npm 包

*   [一饮而尽](https://www.npmjs.com/package/gulp)
*   [大口喘气](https://www.npmjs.com/package/gulp-sass)
*   [吞咽-自动预混合器](https://www.npmjs.com/package/gulp-autoprefixer)
*   [吞咽——丑陋](https://www.npmjs.com/package/gulp-uglify)
*   [胡萝卜散碎](https://www.npmjs.com/package/gulp-hash)
*   [墨西哥湾海岸](https://www.npmjs.com/package/gulp-concat)
*   [gulp-webp](https://www.npmjs.com/package/gulp-webp) 的缩写形式
*   [德尔](https://www.npmjs.com/package/del)

接下来，我们完成了整个包的安装。

```
$ npm install --save-dev gulp gulp-sass gulp-autoprefixer gulp-uglify gulp-hash gulp-concat gulp-webp del 
```

### สร้าง gulpfile.js

我们必须创建一个文件 gulp 以便用来处理我们已经安装的软件包

```
$ touch gulpfile.js 
```

当我们建立一个 gulpfile 的时候。js 完成以下任务，我们将需要执行包调用并创建一个
任务，即将任务分配给 gulp 来处理 ss。

### 回收软件包

```
var gulp                = require("gulp"),
    sass                = require("gulp-sass"),
    autoprefixer        = require("gulp-autoprefixer"),
    uglify              = require("gulp-uglify"),
    hash                = require("gulp-hash"),
    concat              = require("gulp-concat"),
    webp                = require("gulp-webp"),
    del                 = require("del"); 
```

### 为 scss 文件的 com 创建一个任务

作为创建将 scss 转换为 css 的任务的一部分，我们将从删除“t0 }文件夹下的所有文件开始。

随后，我们将从`gulp-autoprefixer`
包中执行`autoprefixer`功能调用，以自动为我们添加预定义的 vendor。

然后，我们通过添加`outputStyle : "comporessed"`office 来编译 scss，以便为我们的 css 执行 minify 操作。

接下来的步骤是将散列添加到我们将要编译的 css 文件名中。
从调用哈希函数开始，为 file 添加代码。

并且，在最后一步中，我们将执行添加密钥、值映射以用于运行 css 文件
到文件夹中的`rev_manifest.json`中。

```
gulp.task("scss", function () {
    del(["static/css/**/*"])
    gulp.src("src/scss/**/*.scss")
        .pipe(autoprefixer({browsers : ["last 20 versions"]}))
        .pipe(sass({outputStyle : "compressed"}))
        .pipe(hash())
        .pipe(gulp.dest("static/css"))
        .pipe(hash.manifest("rev_manifest.json"))
        .pipe(gulp.dest("data"))
}) 
```

### 为 Javascript 编译创建一个任务

作为为文件 Javascript 创建任务的一部分，我们将从删除文件夹下的所有文件开始。【t0 } t0 } t0

接下来，我们将指示 Gulp 处理“t0 }文件夹下的各种文件，它将执行 JavaScript
文件的合并，所有这些文件都提供给它。

然后用`uglify`实现 JavaScript minify，函数由`gulp-uglify`页组成。

接下来的步骤是将散列添加到与我们在上面已经讨论过的 css 相同的文件名中。
从调用 hash 函数开始。

并且，在最后一步中，我们将执行添加密钥、值映射，以便对包含在文件夹中的
脚本执行操作。

```
gulp.task("js", function () {
    del(["static/js/**/*"])
    gulp.src("src/js/**/*.js")
        .pipe(concat('scripts.js'))
        .pipe(uglify())
        .pipe(hash())
        .pipe(gulp.dest("static/js"))
        .pipe(hash.manifest("rev_manifest.json"))
        .pipe(gulp.dest("data"))
}) 
```

### 为编译图像创建任务

作为为我们的映像创建任务的一部分，它从删除“t0 }文件夹下的所有文件开始。

接下来，我们将指示 Gulp 处理文件夹下的各种文件。`src/images`t1 }将对文件夹下的图像文件进行转换。

接下来的步骤是将散列添加到与我们在上面已经讨论过的 css 和 js 相同的文件名中。
从调用 fing 开始。

并且，在最后一步中，我们将执行添加密钥、值映射，以便对包含在文件夹中的
脚本执行操作。

```
gulp.task("images", function () {
    del(["statimg/**/*"])
    gulp.src("simg/**/*")
        .pipe(webp({quality: 50}))
        .pipe(hash())
        .pipe(gulp.dest("static/images"))
        .pipe(hash.manifest("rev_manifest.json"))
        .pipe(gulp.dest("data"))
}) 
```

### 在修改文件时命令 Gulp 更新文件(gulp 观察)

我们将命令 Gulp 在“T0”、`src/js`、`src/js`和`src/images`
文件夹下对所有文件进行检查。

```
gulp.task("watch", ["scss", "js", "images"], function () {
    gulp.watch("src/scss/**/*", ["scss"])
    gulp.watch("src/js/**/*", ["js"])
    gulp.watch("simg/**/*", ["images"])
}) 
```

## [t1⾹关于总线缓存](#%E0%B9%80%E0%B8%81%E0%B8%B5%E0%B9%88%E0%B8%A2%E0%B8%A7%E0%B8%81%E0%B8%B1%E0%B8%9A-cache-busting)

有时，当我们成功地更新网站时，但是用户的浏览器有一个缓存
的存储，使我们已经执行过的文件。

制造一个总线缓存是这个问题的解决方案。对于本文，我们将通过引入从我们调用的函数中派生的散列
来完成一个总线缓存。

一般来说，我会直接将 css、js 和像【t0㎡t1】这样的图片的名称添加到代码本身中，但是在我们使用 CCS 技术的情况下。

接下来，让我们来看一下用这种技术对文件主体名称进行更改的文件检索。

### rev _ manifest 调用。杰森

通过实例，我们在`data`文件夹中存储了`rev_manifest.json``data`
sange，该文件中的数据应具有近似的外观。

```
{"scripts.js":"scripts-efc857a6.js","styles.css":"styles-74c912f3.css","image-1.jpg":"image-1-9f3k238s.jpg","image-2.jpg":"image-2-86s42ds3.jpg"} 
```

众所周知，我们使用 Hugo 创建网站，所以我们将使用 Golang
函数来帮助从【2300】中查找文件名的 index。

通过一个典型的例子，我们将学习这种 css 文件。

```
<link rel="stylesheet" href="http://example.com/css/styles.css" media="all" />
<script src="http://example.com/js/scripts.js" async=""></script> 
```

我们将使用提供的 Hugo Variable 来指向 rev_manifest 文件。json 并找到 index
来实现这种派生值

```
<link rel="stylesheet" href="http://example.com/css/{{ index .Site.Data.rev_manifest "styles.css" }}" media="all" />
<script src="http://example.com/js/{{index .Site.Data.rev_manifest "scriptes.js" }}" async=""></script> 
```

在 Images 方面，我们也使用了相同的方法，通过查找我们希望使用的图的名称 index，
我们可能会创建[shorth。](https://gohugo.io/content-management/shortcodes/)

## 最终代码

当我们把所有的代码组合在一起时，它将成为

gulpfile.js

```
 var gulp                = require("gulp"),
    sass                = require("gulp-sass"),
    autoprefixer        = require("gulp-autoprefixer"),
    uglify              = require("gulp-uglify"),
    hash                = require("gulp-hash"),
    concat              = require("gulp-concat"),
    webp                = require("gulp-webp"),
    del                 = require("del");

gulp.task("scss", function () {
    del(["static/css/**/*"])
    gulp.src("src/scss/**/*.scss")
        .pipe(autoprefixer({browsers : ["last 20 versions"]}))
        .pipe(sass({outputStyle : "compressed"}))
        .pipe(hash())
        .pipe(gulp.dest("static/css"))
        .pipe(hash.manifest("rev_manifest.json"))
        .pipe(gulp.dest("data"))
})

gulp.task("js", function () {
    del(["static/js/**/*"])
    gulp.src("src/js/**/*.js")
        .pipe(concat('scripts.js'))
        .pipe(uglify())
        .pipe(hash())
        .pipe(gulp.dest("static/js"))
        .pipe(hash.manifest("rev_manifest.json"))
        .pipe(gulp.dest("data"))
})

gulp.task("images", function () {
    del(["statimg/**/*"])
    gulp.src("simg/**/*")
        .pipe(webp({quality: 50}))
        .pipe(hash())
        .pipe(gulp.dest("static/images"))
        .pipe(hash.manifest("rev_manifest.json"))
        .pipe(gulp.dest("data"))
})

gulp.task("watch", ["scss", "js", "images"], function () {
    gulp.watch("src/scss/**/*", ["scss"])
    gulp.watch("src/js/**/*", ["js"])
    gulp.watch("simg/**/*", ["images"])
}) 
```

我们已经学习了如何成功地将 Gulp 应用到我们的项目中。【t0 }这将使我们的项目的大小不会在上传的时间内膨胀很多。