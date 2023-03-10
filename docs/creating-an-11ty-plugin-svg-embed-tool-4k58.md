# 创建 11ty 插件- SVG 嵌入工具

> 原文：<https://dev.to/brob/creating-an-11ty-plugin-svg-embed-tool-4k58>

这是[有据可查的](https://bryanlrobinson.com/blog/2019/06/10/routing-contact-form-submissions-to-different-addresses-with-netlify-zapier-and-sendgrid/)T2 我喜欢 JAMstack 。这也是[有据可查的](https://bryanlrobinson.com/blog/2019/05/31/create-11ty-theme-from-static-html-template/)，我是 JavaScript 静态站点生成器 [11ty (Eleventy)](https://11ty.io) 的粉丝。在我最近用它建立的网站中，我发现自己重复使用了几个过滤器。这需要我大量复制和粘贴代码。解决办法？创建一个 11ty 插件。

老实说，我认为这是一个相对较新的特性，但事实证明，它从 v0.2.13 就已经存在了！看看我脸上的馅饼！

> 不关心创建过滤器或插件，只想在你的模板中嵌入一个 SVG？运行`npm install --save eleventy-plugin-svg-contents`和[阅读这里的用法](https://www.npmjs.com/package/eleventy-plugin-svg-contents)。

## 什么是 11ty 插件？

11ty 插件是一个 NPM 包，可将附加内容导出到您的 11ty 配置中。所以你所要做的就是把插件添加到你的站点上，然后把插件添加到你的配置中。

在这一点上，你可以访问插件提供的任何东西。大多数插件都添加了自定义过滤器、标签或短代码。这对于很多用途来说都很方便。官方文档上提供的一些插件[给你方便的](https://www.11ty.io/docs/plugins/)[RSS 源过滤器](https://www.npmjs.com/package/@11ty/eleventy-plugin-rss)，[给你博客文章的估计阅读时间](https://www.npmjs.com/package/eleventy-plugin-reading-time)，甚至[基于包容性语言 lint 你的博客文章](https://www.npmjs.com/package/@11ty/eleventy-plugin-inclusive-language)！

## 新插件:从文件中嵌入一个 SVG

在我的项目中，我喜欢尽可能将 SVG 文件嵌入到 HTML 中，以允许使用 CSS 来控制它们的各个方面。我可能会使用模板`include`将代码放入我的模板中。这样做的缺点是，如果我想使用 CMS 来上传图像，或者在构建过程中使用 SVG 优化器。

这允许我上传 SVG 到我的图片目录，并且仍然可以很容易地把它们嵌入到我的模板中。

先说滤镜本身是怎么工作的。

## SVG contents 过滤器

过滤器背后的逻辑非常简单。

1.  接受文件路径的字符串

2.  在项目中找到该文件。

3.  获取文件的内容

4.  返回编码为 utf-8 的内容

在 11ty 中，我们将自定义过滤器添加到我们的`.eleventy.js`配置文件中，但是我们可以将这些过滤器放在其他地方。

我的配置文件看起来像这样:

```
module.exports = function(config) {  
   config.addFilter("svgContents", require("./filters/svgContents.js"));
   // The rest of my config down here
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我将过滤器本身作为一个`module.export`放在`contents.js`文件中。

```
const fs = require('fs');  

module.exports = function(file) {  
  let relativeFilePath = `.${file}`  

  let data = fs.readFileSync(relativeFilePath, function(err, contents) {  
    if (err) return err  
    return contents  
  });

  return data.toString('utf8')  
} 
```

Enter fullscreen mode Exit fullscreen mode

为了在模板中使用我们的过滤器，我们将使用下面的语法(在 Nunjucks 和 Liquid 中):`/string/to/svg.svg`。

默认情况下，11ty 将接受模板中变量的值作为函数的第一个参数。

然后，我们将该字符串传递给节点文件系统`readFile`方法。这将返回文件的内容。然后，我们将返回我们的过滤器方法与我们的内容值编码为 UTF-8 为我们的模板。

过滤器到此为止。它相当轻便。如果您愿意，您可以将该代码复制并粘贴到您的项目中，并嵌入您想要的任何 SVG。就像我上面说的，我厌倦了复制和粘贴。让我们来看看需要什么才能使它成为一个插件！

## 为 11ty 创建插件

11ty 的插件的核心是 JavaScript 模块。在使其可共享的情况下，它特别是一个 NPM 包。

有比我更好的人来解释为 NPM 模块正确设置 package.json 的复杂性，但这里重要的是，我们模块的主文件是它自己的`.eleventy.js`配置文件。

我们的 11ty 配置文件的内容应该看起来非常相似:

```
const svgContents = require("./src/getSvgContents");  

module.exports = function(eleventyConfig) {  
    eleventyConfig.addFilter("svgContents", svgContents);  
}; 
```

Enter fullscreen mode Exit fullscreen mode

文件路径已经改变，但理论仍然是相同的。我们将过滤器添加到`eleventyConfig`对象中。我们模块的内容将在最终使用时注入到主 11ty 配置文件中。

当我们做一些小的改动时，让我们为代码添加一些保护:

```
const fs = require('fs');  
const path = require('path');  

module.exports = function(file) {  
    let relativeFilePath = `.${file}`  
    if (path.extname(file) != '.svg') {  
        throw new Error("eleventy-plugin-svg-contents requires a filetype of svg");  
    }  
    let data = fs.readFileSync(relativeFilePath, function(err, contents) {    
        if (err) {  
            throw new Error(err)  
        }  

        return contents  
    });  
    return data.toString('utf8')  
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经在流程的早期添加了错误检查。如果文件扩展名不是`.svg`，我们将退出并在控制台显示一个错误。知道你的站点构建失败的原因总是很有帮助的。

在代码的后面，我们像在初始过滤器中一样使用`fs.readFile` API 的错误条件，但是将它转换成一个完整的错误。

就是这样。我们将它发布到 NPM，现在我们可以将它安装到我们的项目中。

## 安装插件

### 1。安装软件包

```
npm install --save eleventy-plugin-svg-contents 
```

Enter fullscreen mode Exit fullscreen mode

### 2。需要我们的配置文件中的包

```
const svgContents = require("eleventy-plugin-svg-contents"); 
```

Enter fullscreen mode Exit fullscreen mode

### 3。注册插件

```
module.exports = function(config) {  
   config.addPlugin(svgContents);  

// Everything else
} 
```

Enter fullscreen mode Exit fullscreen mode

### 4。使用我们模板中的过滤器

在任何 Nunjucks 或 Liquid template 中，使用一个变量符号:`/string/to/svg.svg`。

## 11ty 让这一切变得超级简单有趣

如果您已经制作了一个 11ty 过滤器或自定义标签，您可以将它们抽象出来打包，以便在项目之间携带您喜欢的配置选项。

你不再需要一个 SVG 嵌入过滤器，我已经为你做好了准备。您还想创建其他什么过滤器？如果你不想创建一个自定义的过滤器或标签，你最常用的过滤器和插件是什么？你能创建自己的包来打包它们，给自己一个新项目的良好开端吗？

请在评论中告诉我！

### 实用 CSS 网格——了解这场网页设计革命！

无论您是 CSS Grid 的新手还是玩家，找到这种新布局机制的实际例子是了解其威力的最佳方式。在下面注册两个小时专门为您提供的实用网格知识！

现在就开始学习吧！