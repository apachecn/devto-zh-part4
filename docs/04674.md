# 使用 webpack 和 Hugo 来管理静态网站的资产

> 原文：<https://dev.to/kazushikonosu/use-webpack-with-hugo-to-manage-assets-for-your-static-website-2172>

我最近用 Hugo 更新了我的个人网站`gadgetlunatic.com`。虽然 Hugo 是一个从 markdown 文件构建 HTML 文件的很棒的工具，但是其他所需文件的情况就不同了。

Hugo 确实提供了一种使用[管道](https://gohugo.io/hugo-pipes/)管理 Javascript 和 CSS 文件的方法，但是功能有限。与当今前端开发中使用的工具相比，Hugo 用于处理资产的生态系统缺少功能(和第三方附加组件)。

Webpack 在前端开发人员中非常流行，用来管理网站的资产文件。它不仅捆绑了 Javascript 文件，还能够管理图像和样式表。它现在是使用 React 或 Vue 等前端库/框架的项目的必备工具。

对我来说，结合这两种工具是一个自然的决定，但是没有结合使用这两种工具的例子。我将重点介绍结合 Hugo 和 Webpack 创建一个具有现代资产管理的静态网站的过程。

## 注

本文假设您在`layouts/`和`static/`下创建了自己的主题文件；我们不会使用`themes/`下的公共主题

另外，我也不会详细介绍如何用 Hugo 创建一个静态网站。

## 目标

本指南的目标是使用 webpack 和 Hugo 来捆绑和构建静态网站中使用的资产。以下是我们将要详细做的一些事情。

*   捆绑 Javascript /样式表文件。
*   处理图像并根据哈希更改文件名。
*   对捆绑文件使用哈希文件名以优化缓存。
*   在开发期间从内存中提供 JS/CSS 文件，以减少磁盘写入。

## 目录结构

```
├── README.md
├── archetypes
├── config.toml
├── content # where the article markdown files will be stored
├── data # ignored by git
│   └── manifest.json
├── layouts
├── node_modules  # ignored by git
├── package.json
├── public # Where the final files will be built to; ignored by git
├── resources
├── src # The files used for the website
│   ├── assets
│   ├── js
│   └── scss
├── static # ignored by git
├── webpack.config.js 
└── yarn.lock 
```

我们将在`src/`下管理 Javascript 和 SCSS 文件。这里的文件将由 webpack 处理并构建到`static/`。一旦处理完毕，webpack 就会发出`data/manifest.json`。Hugo 将在构建时引用该文件。

用于发布的最终文件将写在`public/`下

## 1。安装ˌ使成形

### Webpack

首先，让我们设置 webpack。这个示例设置将 Javascript 和 SCSS 文件分别捆绑在`src/js`和`src/scss`下。它还可以处理`src/assets`下的图像。

我们将使用 [`webpack-manifest-plugin`](https://github.com/danethurber/webpack-manifest-plugin) ，这将在构建时产生`data/mainfest.json`；该文件将包括使用哈希的捆绑文件的文件名。

另外， [`webpack-dev-server`](https://github.com/webpack/webpack-dev-server) 用于在开发过程中从内存中托管捆绑文件。

```
// ./webpack.config.js

const path = require('path')
const autoprefixer = require('autoprefixer')
const cssnano = require('cssnano')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const ManifestPlugin = require('webpack-manifest-plugin')

module.exports = env => {
    const isProduction = Boolean(env && env.production)
    console.log('Production: ', isProduction)

    return {
        mode: isProduction ? 'production' : 'development',
        devtool: isProduction ? 'source-map' : 'inline-source-map',
        entry: path.resolve(__dirname, 'src/js/index.js'),
        output: {
            path: path.resolve(__dirname, 'static/'),
            filename: `js/${isProduction ? '[hash].' : ''}bundle.js`,
            publicPath: '/'
        },
        plugins: [
            new MiniCssExtractPlugin({
                filename: `css/${isProduction ? '[hash].' : ''}bundle.css`
            }),
            new ManifestPlugin({
                fileName: '../data/manifest.json'
            })
        ],
        devServer: {
            port: 1314
        },
        module: {
            rules: [
                {
                    test: /\.js$/,
                    exclude: /node_modules/,
                    loader: 'babel-loader'
                },
                {
                    test: /\.scss$/,
                    use: [
                        {
                            loader: MiniCssExtractPlugin.loader
                        },
                        {
                            loader: 'css-loader',
                            options: {
                                importLoaders: 2,
                                sourceMap: !isProduction
                            }
                        },
                        {
                            loader: 'postcss-loader',
                            options: {
                                sourceMap: !isProduction,
                                plugins: [
                                    cssnano({ preset: 'default' }),
                                    autoprefixer({ grid: true })
                                ]
                            }
                        },
                        {
                            loader: 'sass-loader',
                            options: {
                                sourceMap: !isProduction
                            }
                        }
                    ]
                },
                {
                    test: /\.(png|jpg|gif|svg)$/,
                    loader: 'file-loader',
                    options: {
                        outputPath: 'assets/'
                    }
                }
            ]
        }
    }
} 
```

### 雨果

这里是一个样本布局文件的一部分。在开发过程中，Hugo 将从`webpack-dev-server`加载捆绑资产，运行`localhost:1314`。

当我们构建静态文件发布时，Hugo 会引用 Webpack 发出的`data/manifest.json`来找出捆绑文件的名称。

```
<!-- ./layouts/baseof.html -->

{{ .Scratch.Set "css" "http://localhost:1314/css/bundle.css" }}
{{ .Scratch.Set "js" "http://localhost:1314/js/bundle.js" }}
{{ if not .Site.IsServer }}
{{ .Scratch.Set "css" (index .Site.Data.manifest "main.css") }}
{{ .Scratch.Set "js" (index .Site.Data.manifest "main.js") }}
{{ end }}
<!DOCTYPE html>
<html>
    <head>
        <!-- Omitted　-->
        {{ $css := .Scratch.Get "css" }}
        <link rel="stylesheet" href="{{ $css }}"></link>
        {{ template "_internal/opengraph.html" . }}
    </head>
    <body>
    <!-- Omitted　-->
        {{ $js := .Scratch.Get "js" }}
    <script src="{{ $js }}"></script>
    </body>
</html> 
```

### NPM 剪子

以下是用于此设置的 npm 脚本。

```
 "scripts":  {  "start":  "webpack-dev-server & hugo server",  "build":  "webpack --env.production && hugo --minify"  } 
```

## 2。发展

运行`yarn start`，那么`webpack-dev-server`和`hugo server`都会启动。`localhost:1313`上的 hugo 服务器将使用从`localhost:1314`托管的捆绑文件。

## 3。建设

运行`yarn build`。First webpack 将捆绑和处理`src/`下的资产。一旦完成，它还将产生`data/mainfest.json`。之后 hugo 会把文件建到`public/`。Hugo 将使用`mainfest.json`找出包文件的名称。

## 就是这样！

我希望这对那些想和 Hugo 一起使用 webpack 的人有所帮助。也检查一下我的 [Github 库](https://github.com/kazushisan/gadgetlunatic)，它包含了这个设置的运行版本。

感谢阅读我的第一篇帖子！如果有任何问题，请随时提问，我将很乐意回答🙂