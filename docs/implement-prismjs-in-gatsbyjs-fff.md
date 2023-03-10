# 在 GatsbyJS 中实现 PrismJS

> 原文：<https://dev.to/niklasmtj/implement-prismjs-in-gatsbyjs-fff>

我最近在 [GatsbyJS](https://www.gatsbyjs.org/) 中为代码高亮实现了 [PrismJS](https://prismjs.com/) 。因为没有那么多帖子来做快速安装，所以我就在这里做了。

## 安装棱镜

首先，安装 PrismJS 和它的盖茨比插件:

```
npm install --save gatsby-transformer-remark gatsby-remark-prismjs prismjs 
```

这将确保你有所有需要的插件。接下来，转到你的`gatsby-config.js`，把下面的代码放在你的`gatsby-transformer-remark`声明之后，因为这是一个插件，它必须像这样放在插件部分:

```
// gatsby-config.js
resolve: `gatsby-transformer-remark`,
      options: {
        plugins: [
            {
          resolve: `gatsby-remark-prismjs`,
          options: {
            aliases:{sh: "bash", js:"javascript"},
            showLineNumbers: true,
          }
        }],
      }, 
```

## 选择一个 PrismJS 主题

在那之后，你必须在你的`Gatsby-browser.js`文件中包含你喜欢的 [PrismJS 主题](https://github.com/PrismJS/prism/tree/1d5047df37aacc900f8270b1c6215028f6988eb1/themes),也要包含像这样的行号

```
// gatsby-browser.js
require("prismjs/themes/prism-tomorrow.css");
require("prismjs/plugins/line-numbers/prism-line-numbers.css"); 
```

### 可选:原子编辑器样式

因为我不太喜欢 PrismJS 自带的默认主题，所以我寻找了一个我更喜欢的主题。我在 [GitHub](https://github.com/PrismJS/prism-themes/) 的附加主题中找到了我喜欢的 [Atom 编辑器](https://atom.io/)风格。要包含它，请使用 npm 安装`prism-themes`。

```
npm i prism-themes 
```

并将您的`gatsby-browser.js`改为:

```
// gatsby-browser.js
require("prism-themes/themes/prism-atom-dark.css");
require("prismjs/plugins/line-numbers/prism-line-numbers.css"); 
```

## 改变你的 CSS

由于行号的原因，你的 CSS 必须有所改变。我的在`/components/layout.css`。在那里，你必须添加下面的代码行来使一切有序:

```
.gatsby-highlight {
  background-color: #1d1f21;
  border-radius: 0.3em;
  margin: 0.5em 0;
  padding: 1em;
  overflow: auto;
}

.gatsby-highlight pre[class*="language-"].line-numbers {
  padding: 0;
  padding-left: 2.8em;
  overflow: initial;
} 
```

就是这样。您刚刚在 GatsbyJS 中实现了 PrismJS，用 Atom 编辑器样式突出显示了您的代码块。

PS:我还更新了我的 [GatsbyJS starter](https://github.com/niklasmtj/gatsby-starter-julia) 使用 PrismJS。

感谢您的阅读，
尼克拉斯

* * *

这篇文章首先发表在我的博客上: [niklasmtj.de](https://niklasmtj.de/blog/prismjs-in-gatsbyjs)

由 [Unsplash](https://unsplash.com) 上的[émile Perron](https://unsplash.com/@emilep?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄的照片