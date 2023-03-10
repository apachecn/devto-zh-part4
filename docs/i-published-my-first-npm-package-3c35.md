# 我发表了我的第一个 NPM 包裹！

> 原文：<https://dev.to/lluiscamino/i-published-my-first-npm-package-3c35>

在开发我的网站时，我发现自己在寻找一种解决方案，它能给我 GitHub 为每种编程语言定义的颜色，这样我就可以用显著的颜色来设计帖子中的标签，类似于 Dev.to 所做的:
[![](img/81490779dd12fa97eabef2135cef838e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AEwwenAF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1di8gwkdyi9gf0u3xr0n.png)

虽然已经有一些完成这项工作的包，但我决定不使用其中的一个，而是创建一个更简单的包，只返回一种语言的颜色，而不是 GitHub 也提供的我不需要的其他数据。

因此，使用这个 JSON 文件，我创建了一个可能做到的最简单的 NPM 包:一个返回任何编程语言颜色的包。

我还做了一个原型来展示这个包是如何工作的:
[![](img/f64c2c3875a945d8618d7c89060205cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AU8mgwhb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0jsewveekbt83anajuxy.gif)

你只需要输入你想要的语言，点击回车，它就会以自己的颜色显示出来。🤗

我知道这是一个简单的包，它可能不是很有用，这里有 NPM 和 GitHub repo 的链接(包含安装和使用说明)，以防有人需要它。

*   [NPM 套餐](https://www.npmjs.com/package/github-lang-colors)
*   [GitHub 库](https://github.com/lluiscamino/github-lang-colors)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[lluiscamino](https://github.com/lluiscamino)/[github-lang-colors](https://github.com/lluiscamino/github-lang-colors)

### 轻松获得一门编程语言的色彩！👨‍💻

<article class="markdown-body entry-content container-lg" itemprop="text">

# github-lang-颜色

[![npm version](img/8bae73c1c96d2e7dc96aadcc59277353.png)](https://www.npmjs.com/package/github-lang-colors)

返回 GitHub 为任何编程语言定义的颜色的简单包。

## 装置

通过国家预防机制:

```
npm i github-lang-colors
```

## 使用

```
const getColor = require('github-lang-colors');
console.log(getColor('JavaScript')); // #f1e05a
console.log(getColor('PHP')); // #4f5d95
```

注意，该函数不区分大小写。

```
console.log(getColor('php')); // #4f5d95
```

## 语言颜色源

*   [ozh/github-colors](https://github.com/ozh/github-colors)

</article>

[View on GitHub](https://github.com/lluiscamino/github-lang-colors)