# 创造一道彩虹

> 原文：<https://dev.to/terabytetiger/creating-a-vue-rainbow-22be>

这篇文章将涵盖:

*   `v-for`简要概述
*   使用`v-for`显示数组中的项目
*   向项目添加交替颜色
*   向项目添加多种颜色

*注意:我将在这里对所有代码片段使用`<template>`语法*

## 演示

将涵盖的最终组件示例:

[https://codesandbox.io/embed/v-for-example-zeusm](https://codesandbox.io/embed/v-for-example-zeusm)

# V-为引子

在 Vue 中，您经常会发现自己想要显示一个项目列表。Vue 有一个优雅的解决方案，它允许你利用`v-for`指令重复一个 HTML 块( [Vue 的 v-代表完整的文档](https://vuejs.org/v2/guide/list.html))。

这是一个用于条目对象数组的`v-for`的通用模板:

```
<ul>
    <li v-for="(item, index) in itemList" v-bind:key="index">
        {{index}} - {{item}}
    </li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

看起来像这样:

[https://codesandbox.io/embed/v-for-example-he36t](https://codesandbox.io/embed/v-for-example-he36t)

关于这段代码，有三点需要注意:

1.  不需要导入，但是这样做将使你能够容易地引用当前项在数组中的位置。
2.  往往`v-bind:`会简称为`:`。在这种情况下`v-bind:key="index"`可以写成`:key="index"`
3.  `key`被用作 Vue 的一个标识符，这样如果你的列表中的一个项目需要重新呈现，它可以每次只更新需要它的特定项目，而不是整个列表！

关于`v-for`更详细的介绍，我强烈推荐查看这里:

## 文章不再可用

# 建筑构件

现在我们已经显示了一个项目列表(见上面的例子)，我们将添加静态 CSS(我们将在未来的组件中构建)。

## 静态颜色分量

在我们开始这项奇特的工作之前，我们将从基础开始。由于我们将在接下来的两个组件中做什么，我们将把下面的内容添加到我们的`<li>`元素中:

```
<li 
    v-for="(item, index) in itemList" 
    v-bind:key="index"
+   v-bind:style="{
+       backgroundColor: 'firebrick',
+       color: 'white'
+   }"
> 
// I also added the following to the to make it feel less dense //
<style> +li {
+  padding: 5px;
+} </style> 
```

Enter fullscreen mode Exit fullscreen mode

让我们一行一行地回顾我们的更改:

这为 style 创建了一个 vue 绑定，允许我们引用我们的数据和/或项目值来设置我们的样式。在这种情况下，我们将使用[对象语法](https://vuejs.org/v2/guide/class-and-style.html#Object-Syntax-1)。

Vue 对 CSS 属性使用 camelCase，而不是 kebab-case。现在，我们静态地将属性值设置为“firebrick”

`color: 'white'` -与背景颜色相似，我们静态地将字体颜色指定为“白色”。

`}"` -关闭我们的对象！

此时，我们的组件将如下所示:

[https://codesandbox.io/embed/v-for-example-tshp5](https://codesandbox.io/embed/v-for-example-tshp5)

## 交替颜色分量

现在，让我们来增加情趣，改变颜色吧！对于第一个例子，我们将使用一个颜色数组在`rebeccapurple` ( `#663399`)和`firebrick` ( `#B22222`)之间交替。出于演示的目的，我将混合一个 hexcode 和一个 CSS 颜色名称。

`<template>`更新:

```
<li 
    v-for="(item, index) in itemList" 
    v-bind:key="index"
    v-bind:style="{
-       backgroundColor: 'firebrick', +       backgroundColor: colors[index % 2],
        color: 'white'
    }"
> 
```

Enter fullscreen mode Exit fullscreen mode

`<script>`更新:

```
data() {
    return {
-       itemList: ["Item A", "Item B", "Item C", "Item D", "Item E"] +       itemList: ["Item A", "Item B", "Item C", "Item D", "Item E"],
+       colors: ["rebeccapurple", "#B22222"]
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

逐行审查:

创建一个我们想要循环使用的颜色数组作为我们的背景色。注意，不像在 CSS 中，这些需要包装在`"`中，以将值设置为字符串。

`backgroundColor: colors[index % 2]`

*   如果你以前从未见过[模(或余数)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Remainder)操作符，你可能会觉得奇怪。这里所发生的是从前面的数字中去掉`%`之后的数字，直到它不能被减去，从而产生一个非负数。

即)`7 % 3`=>`7 - 3 = 4`=>=`4 - 3 = 1`=>3 不能从 1 中移除，所以`7 % 3`会返回`1`

*   背景颜色将根据我们的颜色数组返回的值来设置。在这种情况下，colors[0]将返回`rebeccapurple`，colors[1]将返回`#B22222`。

当前状态:
[https://codesandbox.io/embed/v-for-example-dn9cb](https://codesandbox.io/embed/v-for-example-dn9cb)

## 彩虹分量

现在我们有了交替颜色的设置，我们可以对代码做 2 个小的调整来交替我们想要的颜色！

`<template>`

```
<li 
    v-for="(item, index) in itemList" 
    v-bind:key="index"
    v-bind:style="{
-       backgroundColor: colors[index % 2], +       backgroundColor: colors[index % colors.length],
        color: 'white'
    }"
> 
```

Enter fullscreen mode Exit fullscreen mode

`<script>`更新:

```
data() {
    return {
-       itemList: ["Item A", "Item B", "Item C", "Item D", "Item E"], +       itemList: ["Item A", "Item B", "Item C", "Item D", "Item E","Item A", "Item B", "Item C", "Item D", "Item E"], -       colors: ["rebeccapurple", "#B22222"] +       colors: ["rebeccapurple", "#B22222", "black", "navy", "green"]
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

逐行审查:

`colors[index % colors.length]` -这里，我们用`colors.length`替换了 2，这将使用我们的`colors`数组的长度来决定我们想要循环多少种颜色。

这里，出于演示的目的，我将项目列表增加了一倍😉

`colors: ["rebeccapurple", "#B22222", "black", "navy", "green"]` -这将`black`、`navy`和`green`添加到我们要循环的颜色列表中。

最终状态:
[https://codesandbox.io/embed/v-for-example-bu9i1](https://codesandbox.io/embed/v-for-example-bu9i1)

# 结束语

这篇文章是我在[https://gridsomeairtable.netlify.com/](https://gridsomeairtable.netlify.com/)的工作带给你的，我在活动页面上使用这种技术在黄色、蓝色和红色边框中循环！

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)/[grid some-air table-starter](https://github.com/TerabyteTiger/gridsome-airtable-starter)

### 一个 Gridsome 初学者利用极简设计的 Airtable。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 网格体气表启动器

[![Netlify Status](img/cc14411d49b34508c22a54f7d8b8ca24.png)](https://app.netlify.com/sites/gridsomeairtable/deploys)

该启动器旨在作为连接[栅格](https://gridsome.org/)和[气动工作台](https://airtable.com/)的起点/范例。

*注:该启动程序利用 [Moment.js](https://momentjs.com/) 获取日期/时间，如果不需要可以从事件文件中移除(并通过`yarn remove moment`移除)*

## 安装 Gridsome CLI 工具(如果你还没有的话)

`npm install --global @gridsome/cli`

## 创建 Gridsome 项目

1.  `gridsome create my-gridsome-site https://github.com/12vanblart/gridsome-airtable-starter.git`安装该启动器
2.  `cd my-gridsome-site`打开文件夹
3.  `gridsome develop`在`http://localhost:8080`启动本地开发服务器
4.  快乐编码<g-emoji class="g-emoji" alias="tada" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f389.png">🎉</g-emoji> <g-emoji class="g-emoji" alias="computer" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4bb.png">💻</g-emoji>

## 为您的第一个表配置 air table 连接

在您的`gridsome.config.js`文件中，您将看到以下内容:

```
// gridsome.config.js //
module.exports = {
  siteName: "Airtable Starter",
  plugins: [
    {
      use: "@gridsome/source-airtable",
      options: {
        // Add these to a .env file
        // Details on finding these values can be found at:
        // https://gridsome.org/plugins/
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/TerabyteTiger/gridsome-airtable-starter)

如果你在某个地方使用这种技术，我希望你能在下面发表评论，或者发一个链接给我，这样我就可以检查一下了！