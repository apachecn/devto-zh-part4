# 见见你们的新女王

> 原文：<https://dev.to/jaredharbison/dragnet-49m1>

# 

<center>Rails + React + Redux - Pt 4</center>

* * *

这篇文章将关注我在从 Fandom 抓取数据时遇到的一些更具挑战性的任务，然后将数据适当地连接到上一篇文章中建立的模式中。回过头来看，我会在皇后区之前刮季，所以我已经说明了这里的顺序。这篇文章将重点介绍 get_queens 方法的定义，get_seasons 将随后介绍。要旨重评！

* * *

# 

<center>我们开始吧！</center>

* * *

*1。Def get_seasons in season.rb 从 Fandom 中抓取季节名称列表，将每个季节名称连接到每个季节的 Wikipedia 页面的 URL 数组中，然后遍历该数组以。创造！()每个季节的一个实例。*

<center>**see previous post for the gist**</center>

*2。在 queen.rb 中 Def get_queens 从 Fandom 中抓取皇后的名字列表，将每个皇后的名字连接到每个皇后 Fandom 页面的 URL 数组中，然后遍历数组到。创造！()每个女王及其属性的实例(包括引用和琐事的关联。*