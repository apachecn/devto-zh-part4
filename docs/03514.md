# 这是刮季节

> 原文：<https://dev.to/jaredharbison/dragnet-39g3>

# 

<center>Rails + React + Redux - Pt 3</center>

* * *

这篇文章将关注我在从 Fandom 抓取数据并将数据恰当地连接到上一篇文章中建立的模式时遇到的一些更具挑战性的任务。这篇文章将重点介绍 get_seasons 方法的定义，get_queens 方法将随后介绍。gists 被大量评论！

* * *

# 

<center>我们开始吧！</center>

* * *

*1。Def get_seasons in season.rb 从 Fandom 中抓取季节名称列表，将每个季节名称连接到每个季节的 Wikipedia 页面的 URL 数组中，然后遍历该数组以。创造！()每个季节的一个实例。*