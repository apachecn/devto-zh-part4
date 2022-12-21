# 👷更高的类型安全性

> 原文：<https://dev.to/pirixgh/higher-type-safety-for-typescript-4703>

 [![logo](img/1a9c54b74f00c3ca170c9b3b75df7828.png "ts-toolbelt")](https://github.com/pirix-gh/ts-toolbelt) 

## 💣问题是

你有没有发现自己需要改变另一个库中的外部类型，或者只是需要特定的字段成为可选的？

**又或许你只是想更新一个类型，但是深入？**

或者只是简单地将元组连接成一个？

一周前，这是不可能的...直到今天。

## 🤔但是怎么做呢？

如果我告诉你，打字稿的大部分痛苦可以永远抛开，会怎么样？这样你就可以专注于现在重要的事情，而不是绕过 TS 的限制？？

[![](img/7ceef482e7efae07c6819ea39e5c5ebb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N3ilk6Lt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z3m07qv7ivfmkmmaaomt.gif)

这些只是最常见的类型操作的几个例子。

## 🍩那是什么呢？

我很自豪地宣布 [**ts-toolbelt**](https://github.com/pirix-gh/ts-toolbelt) 。一个 **100%测试过的**类型库，使得用 TypeScript 编码甚至**更安全**。但不仅如此，它还会让你的软件更加**健壮**更加**灵活**。

[![](img/bf9156d78d8f008b08fd7498b9d5f2eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rZwF8cqH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gaboujxqehb5k234x3zy.gif)

它使用 TypeScript 的类型系统本身来**计算**更复杂的类型。换句话说，它的 API 公开了用 **CPU & RAM** 换取更高类型安全性的类型。

**ts-toolbelt** 用超过 **150** 个测试类型的集合完成打字稿。它的目标是提高类型正确性，同时为 TS 添加一套全新的**特性**。

## 🥅目标

*   该软件包旨在成为所有**实用程序类型**的主页
*   保持合理的**性能**，这样就不会膨胀 TS
*   计算类型总是**可读的**，就像你输入它一样
*   软件更加类型安全，**灵活** &更加**健壮**
*   给 TypeScript 带来一套全新的额外的**特性**
*   类型可以组合在一起创建更多的类型！

## 🏁入门指南

#### 先决条件

```
npm install typescript@^3.5.0 --save 
```

#### 安装

```
npm install ts-toolbelt --save 
```

#### 你好世界

```
import {Object} from 'ts-toolbelt'

// Merge two `object` together
type merge = Object.Merge<{name: string}, {age?: number}> 
```

该项目是巨大的，但很好地记录了一个网站(和搜索栏)。所以如果你准备好了类型安全， [**这是我的库**](https://github.com/pirix-gh/ts-toolbelt) 。

这是我的第一个开源项目，非常欢迎您的评论😉