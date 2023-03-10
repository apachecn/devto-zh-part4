# 编写简单的 CSS，同时享受 CSS-in-JS 的好处

> 原文：<https://dev.to/kossnocorp/write-plain-css-while-ripping-the-benefits-of-css-in-js-apc>

现代 CSS 领域分为 CSS-in-JS 追随者和普通 CSS 追随者，关于哪种方法更优越的争论一直在持续。尽管有斗争，但它通过推进生态系统并使其更有活力而使我们受益。

普通 CSS 用户获得了成熟的工具、简单性、性能和可移植性，而 CSS-in-JS 用户则享受到了可靠性和与应用程序代码的紧密集成。

## 为什么我们不能两者兼得？

Nyan CSS 是一个约定，允许编写好的 ol' CSS，并将样式作为组件导入(React/Preact/Vue.js)。

**下面是它的工作原理**。首先，你定义 CSS:

```
.Header,
.Text {
  font-family: monospace;
}

.Header {
  font-size: 2rem;
}

.Header-size-large {
  font-size: 2.2rem;
}

.Text-italic {
  font-style: italic;
} 
```

然后你把它作为组件导入:

```
import React from 'react'
import { Header, Text } from './style.css'

function Announcement() {
  return (
    <>
      <Header tag="h1" size="large">
        Welcome Nyan CSS!
      </Header>
      <Text tag="marquee" italic>
        Please, welcome Nyan CSS!
      </Text>
    </>
  )
} 
```

所以你会得到:

[![A page in a browser with large "Welcome Nyan CSS" and moving italic "Please, welcome Nyan CSS!"](img/a086fdc0c5121511cfd8f0ed8fc830ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1D6TL5Ej--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/nyancss/nyancss/c928384e8878e492c24f330b1ce49d66a89808bd/docs/demo.gif)

本质上，Nyan CSS 是一个提供与框架(React、Preact、Vue.js、类名)紧密集成的 convention plus 系列库。你不需要安装任何东西来使用 Nyan CSS 和 HTML。

## 约定俗成

约定很简单，只包含三条规则:

1.  `.Button`是代表一个组件的 CSS 类(其他例子有`.ListItem`、`.UI`等)。)并可作为`<Button>Content</Button>`。
2.  `.Button-disabled`生成一个带有布尔属性的组件(如`.Button-fullWidth`、`.Window-inactive`)，可用作`<Button disabled />`。
3.  `.Button-color-red`表示带有枚举属性的组件(如`.Spacing-direction-column`、`.Window-mode-alert`)，可以用作`<Button color='red' />`。

## 为什么？

## 通用设计系统

因为 Nyan CSS 只是 CSS，所以它可以跨使用不同技术构建的不同项目使用，而无需更改代码。对没有 JS 的静态登录页面使用与 React SPA 相同的样式。

网络技术进化了，时尚和激情改变了，只有少数东西还在，HTML，CSS 和 JS。CoffeeScript 用它的遗产让我们的生活变得更好，但语言本身成为了历史。

## 零开销

由于没有在浏览器中操作 CSS 的运行时，Nyan CSS 通过减少 JavaScript 构建大小并将 CSS 管理留给浏览器来确保最佳性能。

我自己受到了样式化组件的启发，但我正在开发一个小部件，不想增加额外的 15Kb，所以我创建了 Nyan CSS，它的大小不是我使用的框架的 5 倍。

你不需要为你的编辑器安装插件，在使用前转换文件或者解析它。只是 CSS 而已。

## 久经考验

在过去两年中，我逐步改进方法[。我](https://medium.com/dailyjs/js-in-css-df4cf8b9b96c)[用 Nyan CSS 开发了 6 个不同的应用程序](https://gist.github.com/kossnocorp/653f032f4a7cf469c982aa06c290d559)(演示:[日记邮件](https://diaryemail.com/))并且确信**它能正常工作**。

## 我们谈谈吧

你怎么想呢?[试试看](https://github.com/nyancss/nyancss)。然后你可以告诉我好不好；-)