# 那真是一段插曲...

> 原文：<https://dev.to/jaredharbison/dragnet-2nmf>

# 

<center>Rails + React + Redux - Pt 5</center>

* * *

这篇文章将着重于为每一个女王创造一个形象。我将遍历每集的参赛者 id，根据参赛者的名字来查找或创建，然后使用该集和皇后 id 来创建外观。

* * *

# 

<center>我们开始吧！</center>

* * *

*1。Def get_seasons in season.rb 从 Fandom 中抓取季节名称列表，将每个季节名称连接到每个季节的 Wikipedia 页面的 URL 数组中，然后遍历该数组以。创造！()每个季节的一个实例。*

<center>**see previous posts for the gist**</center>

*2。在 queen.rb 中 Def get_queens 从 Fandom 中抓取皇后的名字列表，将每个皇后的名字连接到每个皇后 Fandom 页面的 URL 数组中，然后遍历数组到。创造！()每个女王及其属性的实例(包括引用和琐事的关联。*

<center>**see previous posts for the gist**</center>

*3。实例化季节和皇后后，遍历季节和。创造！()每个女王每集的出场次数及其相应的出场属性。*