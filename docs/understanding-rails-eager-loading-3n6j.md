# 了解 Rails 紧急加载

> 原文：<https://dev.to/johncip/understanding-rails-eager-loading-3n6j>

[备忘单要点](https://gist.github.com/johncip/17c496f04dcd032d594a785e2ad09824)

注意:这是我几年前在 Medium 上发表的一篇文章。我在这里转贴，因为这样可能对某人更有用。

如果你像我一样，没有什么比一堆湿漉漉、热气腾腾的机器生成的 SQL 更让你眼花缭乱的了。我的目标是编写一个 Rails 急切加载指南，重点是动机，而不是日志语句。希望我能给你提供我从未有过的解释。

本文假设您理解模型关联，熟悉连接，并且熟悉 N+1 查询问题。如果这听起来有些奇怪，我推荐[先用](https://sqlzoo.net/)普通 SQL(选择、连接、& WHERE)来做。

(这才是让事情变得简单的真正诀窍。虽然 Active Record 从记录中生成对象的方式足够直观，但是它的查询生成能力并不能让您不必了解查询在数据库中是如何执行的。)

我们开始吧！

## 为什么我们需要急切的加载？

换句话说:为什么 N+1 查询不好？这是因为数据库旅行是昂贵的。查询有开销，事务有开销，与远程数据库服务器对话很慢。这不是你想在循环中做的事情，但是 ORM 让你很容易搬起石头砸自己的脚。

好消息是，如果我们告诉 Active Record 我们计划稍后使用的关联，它可以用少量的查询预加载关联的记录，这样任何循环都可以在已经在内存中的记录上发生。

## `eager_load`:一次查询，带连接

看这个例子:

```
users = User.eager_load(:address)
cities = users.map do |user|
  user.addresses.city
end 
```

使用 SQL 连接的魔力，Active Record 可以通过一个查询获取用户和地址。因为`eager_load`使用了一个[左连接](https://www.zentut.com/sql-tutorial/sql-outer-join/)，你将得到所有的用户，而不仅仅是有地址的用户。

我不会给出完整的 SQL 输出，但是要知道 SELECT 子句包含许多类似于`"user.name" AS t0_r0`的条目。现在你可能已经注意到了`t0_r0`是一个以直角哭泣的表情符号，但是你知道吗，他和他编号的朋友在查询期间使**来自两个可用表的每一列**？这意味着我们可以在或*组*调用的链接*中引用相关模型的任何属性，例如:* 

```
users = User.eager_load(:address).where(address: { city: 'New Haven' })
streets = users.map do |user|
  user.address.street
end 
```

所以我们在一个查询中得到了所有的东西，并且我们的 where 调用工作正常。完事了吗？

也许不是:事实证明，那些工厂-农场`eager_load`查询不仅仅看起来很难看——它们还可能比我们下一个选项产生的查询慢(慢一个数量级或者更多！).这部分是因为连接是昂贵的，部分是因为连接会引入重复，从而增加要创建的对象的数量。

如果连接不在索引列上，Codd 会帮助您。

## `preload`:每表一个查询

这个很简单:AR 为每个被引用的表生成一个查询。缺少连接意味着即使考虑到额外的查询开销，这个过程也会更快。

虽然我们的第一个例子可以很好地用`preload`替换`eager_load`，但是我们的第二个例子，需要在生成的 WHERE 子句中使用`addresses.city`，将会失败。因为用户查询只从用户中选择，所以该查询的 WHERE 子句不能引用地址表中的列。(这里的地址查询不会有帮助，因为它是单独完成的。)

## `includes` : `preload`默认，必要时`eager_load`

这是最常用的方法，如果你已经读到这里，希望它需要最少的解释。当使用`includes`时，默认情况下 AR 单独加载每个表，就像使用`preload`一样。然而，如果你在一个链接的`where`或`group`调用中提到相关的表，`includes`将切换到`eager_load`的连接所有事情的行为**。**

*提示:如果你用 SQL 字符串而不是散列来调用`where`或`group`，你可以使用`references`来触发一个连接。*

## `joins` : \_(ツ)_/

不出所料，`joins`也生成了一个使用连接的查询(这次是内部连接)。

这里的主要优点是您可以对生成的查询进行更多的控制。记住`eager_load`和`includes`将从连接的表中返回每一列的**。就算叫`select`也是如此！**

当结果集要求时，或者当一个`where`依赖于相关表中的列，但不希望拖动那些表的其余列时，您通常会使用`joins`。

因此，尽管`joins`倾向于出现在关于急于装载的文章中，**主要用于*限制*装载什么**。它可以与`select`配合使用，用于一些手术负荷。我们来看一个:

```
users = User.joins(:address).select('addresses.city AS city')
cities = users.map do |user|
  user.city
end 
```

这里，`city`成为用户实例上的一个新方法，因为我们在参数 select 中使用了一个 AS 别名。

提示:这将导致一些对象拥有同类中其他对象没有的方法。这可能会导致混乱的代码——您可以通过在模型类上定义额外的方法来减轻这种情况。这样，预加载只是一个可选的加速，而不是使调用工作所必需的。不过，现在可能是引入查询对象的好时机。

注意，对于`joins`,“选择+别名→新方法”技巧是**使用预先加载的值**的唯一方式。在那个循环中调用`user.address.city`将会忽略内存中的值，并根据用户数量触发新的查询。

## 其他优化

区分哪些加速来自活动记录的哪些部分可能会令人困惑。为了完整起见，让我们看看 AR 用来避免查询的几个不相关的技巧。

首先，AR **使用代理对象来表示查询**，只有当您调用类似`all`、`first`、`count`或`each`的方法时，才会用结果集替换它们。这种设计也使我们能够通过链式调用来构建查询。

AR 还缓存最近查询的结果。如果多次触发同一个查询，则只联系数据库一次。(当日志显示缓存而不是加载时，结果是从内存而不是数据库中读取的。)

不幸的是，这两种方法都不能解决 N+1 问题，应用程序代码中的循环会触发多个*唯一的*查询。为了解决这个问题，我们仍然需要加速装载。

## 最终提示

*   通常有多种方法来获取相同的数据。理解每一个意味着你可以选择最快的，或最可读的。
*   进行基准测试时，尝试使用类似于您的生产环境的设置。本地数据库的每次查询开销比远程数据库小得多。
*   您还应该对您打算连接的列进行索引，但是请注意，索引并不是万能的。索引会降低插入和更新的速度，甚至会降低读取的速度(如果“存储顺序”足够的话，例如获取整个表)。
*   当急切加载仍然不够时，一个有用的模式是构建一个连接一个模型到另一个模型的散列映射，并使用该映射“手动”查找相关的值。这在[演示者/查看对象](http://blog.jayfields.com/2007/03/rails-presenter-pattern.html)中特别有用。
*   也有像 [goldiloader](https://github.com/salsify/goldiloader) 这样的库，它们试图自动化 AR 提供之外的急切加载。
*   当您只想要某些列时，使用 [`pluck`](https://apidock.com/rails/ActiveRecord/Calculations/pluck) 。

## 建议阅读

*   Rails Guides: [活动记录查询接口:急切加载关联](http://guides.rubyonrails.org/active_record_querying.html#eager-loading-associations)
*   BigBinary: [Preload，Eagerload，Includes 和 Joins](http://blog.bigbinary.com/2013/07/01/preload-vs-eager-load-vs-joins-vs-includes.html)
*   Wendi 的博客:[Rails 中的 preload，eager_load，includes，references，and joins](http://blog.ifyouseewendy.com/blog/2015/11/11/preload-eager_load-includes-references-joins/)
*   AkitaOnRails: [用 Rails 2.1 滚动——第一个完整教程——第二部分](http://www.akitaonrails.com/2008/5/26/rolling-with-rails-2-1-the-first-full-tutorial-part-2)
*   阿皮多克: [`references`](http://apidock.com/rails/ActiveRecord/QueryMethods/references)