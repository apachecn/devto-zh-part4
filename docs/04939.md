# 准备好大量的污物

> 原文：<https://dev.to/jaredharbison/dragnet-4kki>

# 

<center>Rails + React + Redux - Pt 2</center>

* * *

这篇文章将重点介绍项目的初始 Rails M&C 方面的设置。V 方面将在后面的帖子中由 React 处理。下面的帖子将重点介绍一些用于初学者数据的抓取。

* * *

# 

<center>我们开始吧！</center>

* * *

我使用脚手架生成器来初始化我的模型、控制器、串行化器和路由。api 标签确保 scaffold generator 不会创建典型的 Rails 视图文件等。Scaffold 仍然会生成一些不需要的代码，但是我发现——对我来说——从这个样板文件开始并随着项目的结束而缩减是有帮助的。

`rails g scaffold queen
rails g scaffold season
rails g scaffold episode
rails g scaffold appearance
rails g scaffold trivia
rails g scaffold quotes`

* * *

一个天罗地网女王通过外表有很多情节。外观用于加入女王和剧集，此外还存储该剧集中女王的统计数据。一个女王也有很多琐事和名言。我想通过相关的女王访问剧集、出场、引用和琐事。注意，Rails 将 trivia(复数)改为 trivium(单数)- go-go Rails 语法。