# Drupal 8 中的弹出窗口？没问题！

> 原文：<https://dev.to/innoraft12/popups-in-drupal-8-no-problem-12b>

Drupal 8 已经发布，每个人都对此感到兴奋。有太多的功能和附加组件需要探索。然而，这一个只集中在覆盖/弹出上，即提供一个简单和容易的实现。通过这篇博客，我的目的是解释如何创建一个你自己的弹出窗口的基本实现。

在学习实现 popup 之前，让我们先简单了解一下 drupal 8 路由系统。

如果你已经知道路由系统，你可以跳过这一节，如果你不知道，我假设你至少还知道 Drupal 8 的基础知识。我有一个页面，其中有一些数据的表格显示。在这个页面的顶部，我将提供一个链接，该链接将在弹出窗口中打开一个表单。

现在，让我们从步骤开始。

首先，我们创建一个路由链接，显示一个包含一些内容的表格。

好莱坞.查看 _ 电影:

路径:“/电影列表”

默认值:

_title:“电影列表”

_ Controller:“\ Drupal \ Hollywood \ Controller \ movie list::overview”

要求:

_permission:“电影列表”

[阅读更多！](https://www.innoraft.com/blogs/popups-drupal-8-no-problem)