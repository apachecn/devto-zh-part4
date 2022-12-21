# 宣布盖茨比插件社交卡

> 原文：<https://dev.to/andrioid/announcing-gatsby-plugin-social-cards-3p85>

见过 Twitter、Slack 或脸书上的帖子旁边显示的图片吗？想知道他们从哪里来吗？我也是！

这个插件将在构建时为你的(备注)创建自动卡片。

## 宣布

我准备宣布我的新盖茨比插件，盖茨比插件社交卡。

盖茨比社交卡有几个替代品，但我想尝试用 SVG、React 和大多数盖茨比网站已经依赖的 Sharp 库来制作这些卡。

目前这个插件驻留在我的网站上，但是如果有人对它感兴趣，我会把它移到自己的 repo 中。如果有任何问题，请在 Twitter 上联系我。

## 特性

### 背景

你可以在你的帖子上放一个封面，我们会用的。否则，我们将使用您可以指定的默认背景，或者如果失败，我们将使用备用背景。

[![default card design](img/f2819a106af6df30958fdf0d2d222e8b.png "default card design")](///static/34221344967eb655433086323a4b2d01/c35de/default-design.jpg)

### 图案

现在有两种设计可供选择，“卡”和“默认”。但是我们可以以后再扩展。

[![default card design](img/00350e31d18dedc625ec55b6e6f1e06d.png "default card design")](///static/7987758a52d337e319646b91587e15c4/c35de/card-design.jpg)

### 作者图片

如果指定，图像上将显示作者图像。这也是可配置的。

[![default card design](img/e0240e5bd681823dd250c2e85a9277d0.png "default card design")](///static/ac9793437536b1aee7d53ef2f3a1801b/c35de/cover-custom-author.jpg)

### 自定义作者-图片

如果不需要，可以忽略作者图像，或者在配置中自定义。

## 安装

```
yarn add gatsby-plugin-social-cards
# or npm install --save gatsby-plugin-social-cards 
```

Enter fullscreen mode Exit fullscreen mode

## 如何使用

请参见[自述文件](https://www.npmjs.com/package/gatsby-plugin-social-cards)了解如何使用。