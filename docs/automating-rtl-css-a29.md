# 自动化 RTL CSS

> 原文：<https://dev.to/nervewax/automating-rtl-css-a29>

当一个网站需要对阿拉伯语、乌尔都语或希伯来语等语言的从右到左(RTL)支持时，一般规则是所有样式都应该被“镜像”，以便页面流与从左到右(LTR)的体验相匹配。

乍一看，这似乎是一项艰巨的任务，因为所有特定于 left 或 right 的 CSS 都需要与页面的整个布局一起交换。

最好的开始是将`dir="rtl"`添加到标记顶部的开始`<html>`标签。这向浏览器表明文本流应该以相反的方式呈现。

## 自动化

进入 [CSSJanus](https://www.npmjs.com/package/cssjanus) ，这是一个 npm 包，基于一个最初由 google 开发的项目，旨在自动将 LTR CSS 转换为 RTL。

> CSSJanus 是一个 CSS 解析器工具，用于帮助将网站布局从 LTR 转换为 RTL。[…] CSSJanus 将改变大多数显而易见的 CSS 属性名称和它们的值，以及一些不那么显而易见的。
> 
> –<cite>[谷歌代码存档——CSS Janus](https://code.google.com/archive/p/cssjanus/)</cite>

为了使用这个包，我将一个简单的节点脚本放在一起，并将其连接到我的 npm 脚本工作流中。

在我的 [npm 脚本编译器文章](https://dev.to/nervewax/npm-scripts-as-a-build-tool-n4)中，我介绍了使用最少的一次性脚本来自动操作的基本思想。为了让这个脚本就位，只需要将定制脚本添加到主构建脚本的末尾。

作为参考，下面是我为这项工作编写的节点脚本:

```
// Dependencies.
const fs = require('fs');
const path = require('path');
const cssjanus = require('./node_modules/cssjanus');

// Source the ltr stylesheet.
const ltrCss = fs.readFileSync(path.resolve(__dirname, './style.css'), 'utf8')

// Run CSSJanus.
const rtlCss = cssjanus.transform( ltrCss );

// Output rtl css file.
fs.writeFile("./rtl.css", rtlCss, function(err) {
    if(err) {
        return console.log(err);
    }
    console.log("RTL CSS generated.");
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 手动微调

我发现 cssjanus 对大多数风格都很有效，但是在某些情况下，需要一个默认的对立面，所以当 CSSJanus 这样做时，风格可以被交换。

例如，如果用绝对定位`left: 10px`来设置某样东西，那么在同一个选择器中设置`right: auto`允许样式自动交换。

如果样式在普通的 css 中，内置的`/* @noflip */`标志可以用来让 CSSJanus 忽略一个样式。这在 npm 软件包自述文件中有详细说明。

在某些情况下，通过利用 dir 属性可以使用额外的小技巧。它的特异性会更高，很容易覆盖其他样式。

```
[dir='rtl'] {
    float: left;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 进一步阅读

*   [CSS 骏利网络工具](https://cssjanus.github.io/)
*   [Mozilla MDN web 文档-方向属性](https://developer.mozilla.org/en-US/docs/Web/CSS/direction)
*   [HTML 中的 w3.org 结构标记和从右向左文本](https://www.w3.org/International/questions/qa-html-dir)