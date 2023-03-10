# 配置 NEXT 的最简单方法。带 SASS 的 JS

> 原文：<https://dev.to/harveyjones282/the-simplest-way-to-configure-next-js-with-sass-3en>

样式是设计应用程序页面的核心部分。每当我开始在 Next.js 上工作时，我通常会发现自己浪费一些时间在谷歌上搜索如何在我的项目中配置 SCSS。所以，为了节省你的宝贵时间，我决定写下这个问题作为参考。

虽然 Next.js 提供了一个非常复杂的插件 [next-sass](https://github.com/zeit/next-plugins/tree/master/packages/next-sass) 来编译 sass 文件，但是这个插件无法解析**。eot** 和**。woff** 文件。因此，为了解析我们所有的 sass 和字体文件，我们必须在 next-sass 中添加自定义 Webpack 配置。

下一步-将编译好的样式表传递给**。下一个/静态/css。** Next.js 会自动将 CSS 添加到你的 HTML 文件中。

# 第一步:安装依赖项

让我们从安装依赖项开始。

首先，我们需要安装 next-sass。

```
`npm install --save @zeit/next-sass` 
```

Enter fullscreen mode Exit fullscreen mode

Next-sass 插件使用了 node-sass，所以我们也必须安装 node-sass。

```
 `npm install --save @zeit/node-sass` 
```

Enter fullscreen mode Exit fullscreen mode

# 第二步:创建下一个配置文件

现在，在项目目录的根目录下创建 **next.config.js** 文件。Next.js 自动从这个文件中读取所有配置。所以您只需要在这里添加配置并导出它们。

# 第三步:添加自定义配置

现在，我们必须在 next-sass 中添加定制的配置，以使其完全工作，并能编译 **SASS** 和字体文件。

该配置能够解析以下文件类型:
Sass 和 css 文件
字体文件(。eot，。woff，. woff2)
图像文件(。png，jpg，。gif，。svg)

```
const withSass = require('@zeit/next-sass');
const withCSS = require("@zeit/next-css");
module.exports = withCSS(withSass({
   webpack (config, options) {
       config.module.rules.push({
           test: /\.(png|jpg|gif|svg|eot|ttf|woff|woff2)$/,
           use: {
               loader: 'url-loader',
               options: {
                   limit: 100000
               }
           }
       });

       return config;
   }
})); 
```

Enter fullscreen mode Exit fullscreen mode

**太好了！现在，您已经配置了 sass，我也有了永久引用。干杯！**