# 工头负责铁轨和反应

> 原文：<https://dev.to/jaredharbison/dragnet-4420>

# 

<center>Rails + React + Redux - Pt 1</center>

* * *

在我与 Flatiron School 的最后一个项目中，我被提示在 React 中使用 Ruby on Rails。这篇文章将为后续文章重点介绍 Rails 和 React combo 的基本结构。

DRAGnet 最终是一个 Ru Paul 的 Drag Race CRUD 应用程序，我将首先从 [Fandom](https://rupaulsdragrace.fandom.com) 收集数据。我将在本系列的后续文章中讨论数据搜集。

为了让 Rails 和 React 一起工作，我拼凑了文档片段和几个过时的教程。我将在第一篇文章中重新创建这个项目的这一方面，希望能让未来的人生活得更轻松一些。

* * *

# 

<center>我们开始吧！</center>

* * *

api 标签将告诉 Rails 削减一些不必要的 MVC 架构。

`rails new DRAGnet --api`

* * *

api 标签也将消除 respond_to 方法。我需要把它加进去。