# 11ty 降价属性

> 原文：<https://dev.to/iarehilton/11ty-markdown-attributes-2dl3>

有一段时间，我寻找一种方法来尝试向 markdown 添加属性，比如 class 或 id。使用 [11ty](https://www.11ty.io/) 作为我的静态站点生成器，我一直使用 Nunjucks 进行模板化，并且能够以那种方式添加属性。我确信在 Markdown 中一定有做同样事情的方法。幸运的是，11ty 提供了一种将你自己的[库实例](https://www.11ty.io/docs/languages/markdown/#optional%3A-set-your-own-library-instance)添加到 Markdown 的方法。这个例子展示了如何使用表情插件，但我发现 [markdown-it-attrs](https://www.npmjs.com/package/markdown-it-attrs) 插件符合我的目的。这基本上就是我如何将它添加到我的项目中的。

**安装库**

```
npm install --save-dev markdown-it markdown-it-attrs 
```

这将给出添加到项目所需的库，并将它们添加到 package.json

**设置库实例** {。
文章标题}
现在我们要添加 11ty 的库来取货

*。eleventty . js*

```
module.exports = function (eleventyConfig) {
  eleventyConfig.addPassthroughCopy("css");

  / ****************Markdown Plugins******************** /
  let markdownIt = require("markdown-it");
  var markdownItAttrs = require('markdown-it-attrs');
  let options = {
    html: true,
    breaks: true,
    linkify: true
  };
  let markdownLib = markdownIt(options).use(markdownItAttrs);
  eleventyConfig.setLibrary("md", markdownLib);

  return {
    passthroughFileCopy: true,
    dir: {
      input: ".",
      includes: "_includes",
      data: "_data",
      output: "_site"
    }

  };
  / ****************END Markdown Plugins******************** /
  return {
    passthroughFileCopy: true,
    dir: {
      input: ".",
      includes: "_includes",
      data: "_data",
      output: "_site"
    }

  };
}; 
```

**用法示例**

*[example . MD](http://example.md/)T3* 

```
---
title: Template
layout: layouts/base.njk
tags:
    - post    
navtitle: Template
date: 2019-09-05
---
# header {.style-me}
paragraph {data-toggle=modal}

another bit of data {.class #identifier attr=value attr2="spaced value"} 
```