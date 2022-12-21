# Tailwind CSS:在几分钟内完成设置

> 原文：<https://dev.to/stephenafamo/tailwind-css-getting-set-up-in-minutes-3gbg>

[![Tailwind CSS Logo](img/97748b6af9bcac96ff0d2ae061f130cf.png "Tailwind CSS")](https://res.cloudinary.com/practicaldev/image/fetch/s--E6uodaHV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/stephenafamo.com/blog/wp-content/uploads/2019/07/Tailwind-CSS.jpg%3Ffit%3D1500%252C750%26ssl%3D1)

Tailwind CSS 是一个很棒的 CSS 框架。实用第一，完全可定制，只是一种享受。

我已经看到了它，并想自己尝试一下，但是设置说明可能会感觉有点乏味，尤其是如果你不熟悉所有的前端构建工具和 CSS 预处理程序。

所以，在我自己浏览了一遍之后，我决定写一个关于设置 Tailwind CSS 的最快方法的指南。

## 先决条件

*   结节
*   NPM

## 假设

*   您熟悉从命令行工作
*   所有命令都从项目的根目录运行
*   我们的主 CSS 文件在`src/css/app.css`
*   我们希望编译后的文件位于`build/css/app.css`

## 第一步:安装 Laravel Mix

Laravel Mix 是一个伟大的工具，它极大地简化了 webpack 的配置。我以前写过一篇关于它的文章[这里](https://dev.to/stephenafamo/using-laravel-mix-with-webpack-for-all-your-assets-31fp-temp-slug-2499234)。

虽然我们在这里只打算用它来编译 CSS，但是它可以做更多的事情。查看我的[上一篇文章](https://dev.to/stephenafamo/using-laravel-mix-with-webpack-for-all-your-assets-31fp-temp-slug-2499234)和[官方文档](https://laravel-mix.com/docs/4.0/installation)。

```
npm init -y
npm install laravel-mix --save-dev
cp node_modules/laravel-mix/setup/webpack.mix.js ./ 
```

我们还需要给我们的`package.json`
添加一些脚本

```
"scripts": {
    "dev": "NODE_ENV=development webpack --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
    "watch": "NODE_ENV=development webpack --watch --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
    "hot": "NODE_ENV=development webpack-dev-server --inline --hot --config=node_modules/laravel-mix/setup/webpack.config.js",
    "production": "NODE_ENV=production webpack --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js"
} 
```

## 第二步:安装并配置顺风 CSS

现在我们安装顺风

```
npm install tailwindcss
npx tailwind init 
```

接下来，我们将编辑我们的`src/css/app.css`文件来添加顺风:

```
@import "tailwindcss/base";
@import "tailwindcss/components";
@import "tailwindcss/utilities"; 
```

## 第三步:配置 Laravel Mix

我们现在将把`webpack.mix.js`编辑成以下内容:

```
let mix = require('laravel-mix');
mix.postCss('src/css/app.css', 'build/css', [
    require('postcss-import'), // So we can use @import statements
    require('tailwindcss'),
    require('autoprefixer'), // to add prefixes like --webkit
]); 
```

我们正在使用另外两个 PostCSS 插件:`postcss-import`和`autoprefixer`。还有我们的顺风 CSS

## 第四步:编译我们的 CSS

为了编译我们的 CSS，我们有几个选择:

*   在“开发”模式下构建 CSS:`npm run dev`
*   构建*开发*模式**和**观察变化:`npm run watch`
*   为了*生产* : `npm run production`

在开发模式下构建跳过了一些事情，比如缩小。使构建更快，并使调试更容易。

开发时，我只需运行`npm run watch`并编码！

## 第五步:最小化文件大小(可选)

主要的 Tailwind CSS 编译文件比较大。

在生产中使用之前，建议我们删除未使用的 CSS。

### 1.4+版本

我们可以通过添加自定义配置文件来实现这一点。我们把它放在`./tailwind.config.js`。应该是这样的:

```
module.exports = {
    theme: {},
    // Specify the paths to all of the template files in your project
    purge: [
        '../templates/*.html',
        './templates/*.vue',
        './templates/*.jsx',
    ],
    variants: {}
} 
```

当我们通过运行`npm run production`为生产构建 CSS 时，所有未使用的 CSS 类都将被清除，最终文件大小会小得多。

### 1.3 及更早版本

我们可以用一个名为 Purgecss 的 PostCSS 插件来实现。在 Tailwind CSS [文档](https://tailwindcss.com/docs/controlling-file-size)中有一篇关于这个的更详细的文章。

将 Purgecss 添加到我们的 Laravel Mix 配置中非常简单:

首先，我们安装 Purgecss

```
npm install @fullhuman/postcss-purgecss --save-dev 
```

接下来，我们添加并修改我们的`webpack.mix.js`，使其看起来像这样:

```
const purgecss = require('@fullhuman/postcss-purgecss')({
    // Specify the paths to all of the template files in your project
    content: [
        './templates/\*.html',
        './templates/\*.vue',
        './templates/\*.jsx',
    ],
    // Include any special characters you're using in this regular expression
    defaultExtractor: content => content.match(/[A-Za-z0-9-\_:/]+/g) || [] })

let mix = require('laravel-mix');
mix.postCss('src/css/app.css', 'build/css', [
    require('postcss-import'), // So we can use @import statements
    require('tailwindcss'),
    require('autoprefixer'), // to add prefixes like --webkit
    ...process.env.NODE\_ENV === 'production'? [purgecss] : [] // Purge only in production
]); 
```

## 结论

设置 Tailwind CSS 可能看起来很乏味，但使用 Laravel Mix，它可以成为一个非常简单的过程。按照上面的步骤，您应该在几分钟内就可以启动并运行。

让我知道任何问题，或在评论区你可能有额外的提示。

帖子 [Tailwind CSS:在几分钟内设置好](https://stephenafamo.com/blog/tailwind-css-getting-set-up-in-minutes/)首先出现在 [Stephen AfamO 的博客](https://stephenafamo.com/blog)上。