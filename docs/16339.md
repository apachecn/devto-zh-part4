# 使用 PostCSS 用荷兰语编写 CSS

> 原文：<https://dev.to/polypane/writing-css-in-dutch-using-postcss-3f15>

大多数编程、样式和标记语言都以英语为基础，但这并不意味着其他语言也不行。就像荷兰版的 CSS。

[post CSS-Dutch-style sheets](https://kilian.github.io/postcss-dutch-stylesheets/)允许你这样写 CSS:

```
.foo {
    hoogte: 300px;
    ondermarge: 10px;
    tekstgrootte: 20px !belangrijk;
    achtergrondkleur: zwart;
    kleur: wit;
} 
```

它会把它变成:

```
.foo {
    height: 300px;
    margin-bottom: 10px;
    font-size: 20px !important;
    background-color: black;
    color: white;
} 
```

特别喜欢`!belangrijk`。

对于属性和值，翻译列表都可用:

*   [属性](https://github.com/kilian/postcss-dutch-stylesheets/blob/master/docs/Properties.md)
*   [值](https://github.com/kilian/postcss-dutch-stylesheets/blob/master/docs/Values.md)

看看有没有你喜欢的荷兰语:)

我想，如果只是为了让荷兰开发者更好地理解每个属性的功能，翻译一些 CSS 概念可能会有一些好处，但这主要是我在几个小时内为了好玩而做的事情。PostCSS 让这种有趣的事情变得非常简单。

这个插件基于[德语版](https://github.com/timche/postcss-german-stylesheets)做同样的事情，但是是针对德语的。

获取 postcss-dutch-stylesheets 非常简单:

`$ npm install postcss-dutch-stylesheets`

[代码在 Github](https://github.com/kilian/postcss-dutch-stylesheets) 上，欢迎投稿！