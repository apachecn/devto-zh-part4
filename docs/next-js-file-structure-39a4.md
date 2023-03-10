# Next.js 文件结构

> 原文：<https://dev.to/sergiodxa/next-js-file-structure-39a4>

React 对如何组织你的文件没有强烈的意见。类似的 Next.js 有几点意见，创建一个`pages`和`static`目录就这样。

我参与了几个基于 Next.js 的项目，其中一个是这个工具的创造者, [ZEIT](https://zeit.co) 网站。

本文总结了我在基于 Next.js 的应用程序中工作时喜欢创建的文件夹。

```
├── __mocks__
├── __tests__
│   └──  __snapshots__
├── components
│   ├── __snapshots__
│   ├── {name}.js
│   └── {name}.test.js
├── e2e
│   ├── helpers
├── hooks
├── pages
│   ├── _app.js
│   └── _document.js
│   └── _error.js
├── services
├── static
└── utils 
```

Enter fullscreen mode Exit fullscreen mode

## __mocks__

在这个文件夹中，我放置了我将在任何集成或单元测试中使用的已安装模块的模拟。模拟必须与导入的文件同名，在像`next/link`这样嵌套模块的情况下，我们需要创建一个`next`文件夹并将`link.js`文件放入其中。

## __ 测试 _ _

下面是集成测试，每个集成测试都应该匹配 [pages](#pages) 目录中的一个页面。

### __ 测试 _ _/_ _ 快照 _ _

集成测试生成的任何快照都将放在该文件夹中。它是 Jest 在进行快照测试时自动生成的。

## 组件

下面是 React 组件，它们可以在多个页面中重用，或者足够复杂，可以将它们移动到另一个文件中并单独测试。

每个文件应该有一个单个组件的默认导出，尽管在文件内部可能有多个组件。文件名必须使用 kebab-case。

这里的每个组件应该至少有一个快照测试，测试文件应该在这个相同的文件夹中，以相同的文件名加上`.test.js`作为文件扩展名。

### 组件/__ 快照 _ _

与[_ _ tests _ _/_ _ snapshot _ _](#_-_-tests-_-_-/-_-_-snapshots-_-_)相同，只是针对每个组件的单元测试。

## e2e

这里是放置端到端测试的地方。每个文件都应该有被识别为测试的`.test.js`扩展名。任何其他文件都应该被 E2E 测试工具忽略(我个人使用 TestCafe)。

### 帮手

如果 E2E 测试需要一个帮助函数，它应该放在这里。该文件不能以`.test.js`结尾，因为它将被视为测试套件。

理想情况下，这些是在任何 E2E 测试中使用的微小函数，例如`setCookie`函数。

## 挂钩

为应用程序创建的任何自定义钩子都应该放在这里，测试文件是理想的，但不是必需的，因为钩子可以通过使用它们的组件的集成或单元测试来测试。

注意这里的钩子是为多个组件使用的，任何为单个组件使用而创建的定制钩子都应该放在组件文件本身中。

## 页面

这里是应用程序的页面(也称为视图)，每个文件将自动匹配一个路径，如 [Next.js 文档](https://nextjs.org/docs#dynamic-routing)中所述。

`_app.js`是一个特殊的文件，它将成为主要的应用程序组件。

`_document.js`是一个特殊的文件，它将只用于服务器端来呈现应用程序的基本 HTML。

`_error.js`是一个特殊的文件，将用于定制 Next.js 错误页面。

## 静态

应用程序所需的任何静态文件(图像、音频等)。)可以放在这里。

这是一个普通的 Next.js 文件夹，这里没什么特别的。

## utils

在这里我放置了我为我的项目创建的任何实用函数，你可能会从`lodash`、`date-fns`或其他类似的库中导入，但是是专门为我的项目创建的。

## 最后的话

这是我在使用 Next.js 时发现很有用的文件结构，它有助于使用框架。

这并不意味着它是最好的，它可能会在每个项目的基础上得到改进或改变。记住，最好的文件结构是移动文件，直到感觉合适为止。

你用像这样的东西吗？你使用完全不同的东西吗？请在 [@sergiodxa](https://twitter.com/sergiodxa) 通知我。