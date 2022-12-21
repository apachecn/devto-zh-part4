# 向您的 Gatsby 帖子添加发布字段以控制其可见性

> 原文：<https://dev.to/mercatante/add-a-published-field-to-your-gatsby-posts-to-control-their-visibility-1j3o>

## 问题

在把我的网站转换成盖茨比之后，我很快意识到我需要一种方法来管理草稿——我开始写的东西，但出于某种原因还没有准备好公开发布。Gatsby 默认公开你的帖子——一旦你创建了 Markdown 文件并建立了你的站点，你的帖子就可供所有人查看。

## 解？

轻松点。在帖子的 frontmatter 中抛出一个`draft: true|false`标志，更新页面查询，只获取位于`draft === false`的页面，然后就到此为止。或者，也许不是...

## 实解

`draft`标志的问题是它对于这个用例来说不够明确。如果我忘记包含`draft`标志，或者如果它有一个错别字(例如`dratf: false`，这个帖子仍然会被公开。

让我们翻转条件的头部，检查一个`published`标志是否为`true`。如果我忘记添加那个标志，或者有一个打印错误(👋`publisehd`)？在我修好旗子之前，帖子不会出现。这是一个小麻烦，但与我不小心发布了还没准备好发布的东西相比，还不算太糟糕。

## 代码

这是这篇文章的前沿问题——没有什么疯狂的事情发生:

```
---
title: Manage Draft Posts in Gatsby
slug: '/manage-draft-posts-in-gatsby/'
tags: gatsby
published: false
--- 
```

这是我的页面查询:

```
query  {  allMdx(  filter:  {  frontmatter:  {  published:  {  eq:  true  }  }  }  sort:  {  fields:  [frontmatter___date],  order:  DESC  }  )  {  edges  {  node  {  fields  {  slug  }  frontmatter  {  date(formatString:  "MMMM DD, YYYY")  title  }  }  }  }  } 
```

`filter`区块中的那条`frontmatter: { published: { eq: true } }`线就是奇迹发生的地方。

有几件事要记住:

*   向 frontmatter 添加新字段后，您需要重新启动 Gatsby dev 服务器，然后才能查询它们
*   如果你有上一篇文章和下一篇文章的链接，添加一个`published`标志意味着:你需要确保你没有链接到未发表的文章。我将在以后的文章中向您展示我是如何处理这个问题的。朋友们，敬请期待！

👋喜欢这篇文章吗？

加入我的[时事通讯](https://stevemerc.com/newsletter/?r=dev)并在 Twitter[@ mercantate](https://twitter.com/mercatante)上关注我以获得更多类似的内容。