# 使用异步发生器创建打字机效果

> 原文：<https://dev.to/kapantzak/creating-the-typewriter-effect-with-the-use-of-async-generators-5f1e>

在本帖中，我们将展示创造打字机效果的过程。

下面的代码演示了最终的结果。

[https://codepen.io/giannisKapa/embed/ExYwaRL?height=600&default-tab=js,result&embed-version=2](https://codepen.io/giannisKapa/embed/ExYwaRL?height=600&default-tab=js,result&embed-version=2)

# 我们的目标

我们想要实现的功能是我开发的另一个叫做**文本输入**的库(有更多的选项和方法)的子集。它还处于测试版，但是对于任何想尝试它的一些功能的人来说，它可以作为 npm 包使用。

你可以在这里找到**文本输入**:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [卡潘扎克](https://github.com/kapantzak) / [文本输入](https://github.com/kapantzak/text-typing)

### 简单打字效果

<article class="markdown-body entry-content container-lg" itemprop="text">

[![npm version](img/bd85e2d3961a5128bff768ce6703263a.png)](https://badge.fury.io/js/text-typing)

# 文本打字

一个创建打字机效果的工具，有一个简单的，基于承诺的 api。

[![text-typing gif](img/612d472630e90b391c25e811df4b6c4a.png "text-typing")](https://github.com/kapantzak/text-typingimg/text-typing.gif?raw=true)

## 装置

安装 npm 包

#### npm

```
npm install text-typing 
```

#### 故事

```
yarn add text-typing 
```

然后导入该包

```
import {textTyping} from "text-typing" 
```

## 使用

您所需要做的就是初始化这个工具，传递一个对现有 DOM 元素的引用，然后开始输入！

#### 超文本标记语言

```
<h1 id="myHeading"></h1&gt 
```

#### 射流研究…

```
(async () => {      
  const txt = textTyping(document.getElementById("myHeading"));
  await txt.typeText("Hello");
})(); 
```

#### 链接方法

您可以在同一个实例上调用多个方法，或者使用`await`(在`async`函数中)，或者在方法调用后使用`then`

##### 等待

```
(async () => {      
  const txt = textTyping(elem);
  await txt.typeText("Hello");
  await txt.backspace(2);
})(); 
```

##### 然后

```
(() => {
  const txt = textTyping(elem);
  txt.typeText("Hello").then(resp => {
    resp.backspace(2);
  });
})(); 
```

## 选择

### 速度

如果没有为特定的方法提供特定的速度，则被调用的方法将使用的键入速度。

…

</article>

[View on GitHub](https://github.com/kapantzak/text-typing)

在这篇文章中，我们将开发一个函数来公开一个小的 api(一个方法😋)然后，我们可以调用它来应用打字机效果。该函数将接受两个参数:

*   要键入的文本
*   由两个数字组成的数组，将用作打字过程的速度范围

电话应该是这样的:

```
const textHolder = document.getElementById("myTextHolder");
const speedRange = [100, 600];
const txt = textTyping(textHolder, speedRange);
txt.typeText("Hello there!"); 
```

Enter fullscreen mode Exit fullscreen mode

# 标记

首先，我们需要一个 HTML 元素来作为我们的文本容器。让我们使用 id 为`myTextHolder`的`<div>`元素，它嵌套在另一个 div 元素中，该元素将被用作包装器元素。