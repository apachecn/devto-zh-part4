# 向现有 PHP 项目添加前端框架

> 原文：<https://dev.to/dasdaniel/adding-front-end-framework-to-existing-php-project-4lf9>

这是一个关于堆栈溢出问题的答案，我不能发布，因为在我发布之前它已经关闭了。[https://stack overflow . com/questions/57129851/how-to-use-js-frameworks-like-react-and-vue-and-es6-with-a-PHP-project](https://stackoverflow.com/questions/57129851/how-to-use-js-frameworks-like-react-and-vue-and-es6-with-a-php-project)

对于前端框架的新手来说，我认为最简单的方法可能是做一些小的增量更改，用 Vue 替换 jQuery。我会说 Vue，而不是 React，因为从 v. 16 开始的学习曲线和变化使得它对未编译的使用不太友好。React 团队似乎已经对支持这种渐进的方法失去了兴趣，不再支持主包中的`React.createClass`。还是可以的，只是现在多了一个套餐。

您可以通过在 PHP 模板中添加一个新的脚本标签来添加增量修改，如

`<script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script>`

，它将加载除 jQuery 之外的 Vue，直到您完全删除 jQuery)

然后找到一个你想从 jQuery 转换成 Vue 的组件。

现在，您可以将 Vue 模板或组件添加到 PHP 布局/页面中，并将 JS 脚本添加到页面、页面脚本文件或特定于组件的脚本文件中。

冲洗并重复其他组件。

需要注意的是，这并不是最佳实践。理想情况下，您应该有一个 JS 管道来生成 SPA(单页应用程序)并处理来自 PHP 应用程序的 API。这是我开发应用程序的首选方式，但不是必须的。按照我描述的方式做将会增加长期管理应用程序的痛苦，但这可能没有管理 jQuery 那么糟糕。只要您组织好文件，使用这种方法是开始添加一些高级功能的合理方式。如果您知道您将只支持现代浏览器，您可以开始使用模板文字在脚本中定义模板，这将有助于代码管理，因为您可以将 vue 布局保存在 js 文件中，而不是 php 模板中。稍后，随着您越来越熟悉，您可以开始将项目整合到一个统一的 SPA 中，从而获得编译项目的所有好处。