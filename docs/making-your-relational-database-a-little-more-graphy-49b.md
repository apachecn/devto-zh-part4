# 让您的关系数据库更加图形化

> 原文：<https://dev.to/acoh3n/making-your-relational-database-a-little-more-graphy-49b>

说到存储数据，关系数据库一直是我的首选解决方案。它们是可靠的，它们是成熟的，它们的结构化查询语言(或 SQL)是非常强大的，如果你设计并善待它们，它们通常会提供很好的性能。

在关系数据库中，一切都是表。例如，如果你想存储一个客户列表，那么你可能会创建一个`customers`表来存储这些信息。

对于数据库中的每个表，还需要定义要存储在表中每一项上的信息。这些信息在技术上被称为“字段”。

下面是一个非常简单的`customers`表的例子:

```
CREATE TABLE customers (
  id int,
  last_name varchar(255),
  first_name varchar(255),
  email_address varchar(255)
); 
```

Enter fullscreen mode Exit fullscreen mode

如果您还想存储——比如说，客户订单——您可以创建一个`orders`表，其中表中的每一项(或每一行)都是一个订单，带有一个到下订单的客户的链接(或关系)。

```
CREATE TABLE orders (
  id int,
  customer_id int,
  order_date date,
  order_status varchar(10),
  order_total decimal
); 
```

Enter fullscreen mode Exit fullscreen mode

第二个字段`customer_id`是`customers`表中客户的 id，它创建了`orders`表中的条目与`customers`表中的条目之间的关系。

这是一种建模数据的好方法。事实上，许多软件项目就是以这种方式构建的。

最近，一种新型数据库开始变得更加主流:图形数据库。图形数据库采用稍微不同的方法来思考/建模您的数据。

它鼓励您将数据视为一个对象网络，而不是将数据视为一系列表。一个典型的例子就是你的脸书/Linkedin 网络。你连接到某人，那个人反过来连接到另一个人，等等。

在图形数据库中，实际上只有两种类型的东西:

1.  顶点/节点-构成图形元素的基本单位。因此，脸书例子中的“朋友”将是一个节点/顶点。

2.  边-充当节点/顶点之间的链接。

以下是这种情况的一个例子:

[![](img/3bc8a6eeefeb44b2561cf18e36d16b2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T2fp121O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lnafnx3tg1qrsz2149b6.png)

所以黄色的圆圈是节点/顶点，线是边。

从表面上看，这似乎非常类似于关系数据库中的关系概念。这是真的，只要你的对象网络不是太“宽”。一旦有了许多“关系度”(即一个对象链接到另一个对象，以此类推)，SQL 查询就会变得有些难以处理。

当我第一次了解它时，这一切对我来说都很有趣，但因为当时我对它没有任何实际应用，所以我把它放在脑后。

后来有一天，我遇到了一个有趣的问题，这是我过去从未遇到过的:建立一个内容管理系统。让我们先把构建与不构建 CMS 的讨论放在一边。这不是本文的重点。只能说我们别无选择只能造一个。

不过，CMSes 的这一点与传统的客户/订单类型数据库不同，它们通常有一组更丰富的对象。所以在我们的情况下，在电视行业，我们必须存储电影和剧集，连续剧和视频之类的东西。但我们也想直接从我们的 CMS 屏幕上驱动整个体验。

这意味着我们想给我们的编辑在某个时间点定义不同类型用户的主页外观的权力。或者我们想让他们安排一些将出现在主页上的电影(想想网飞式的体验)。或者我们希望基于用户与网站的交互来自动驱动某些电影集合。

纵观这个数据模型，很明显我们手中有一个相当复杂的对象网络。就在这个时候，我想起了我放在脑后的那个图形数据库。这看起来非常适合它。

我对使用“图表”方法的想法很感兴趣，但是除了我那古老的关系数据库之外，我对使用任何东西的想法都不感兴趣。在我使用关系数据库的许多年中，没有一个“真正的”图形数据库看起来具有我喜欢的成熟度和健壮性。

所以我想知道是否有一种方法可以让我鱼与熊掌兼得。或者使用关系数据库，但作为图形数据库。为了实现这一目标，我必须解决几个问题:

1.  我如何存储数据？
2.  我如何查询数据而不用编写复杂而脆弱的 SQL 查询？
3.  性能呢？

第一个问题相对容易解决:

```
create table node (
  id          varchar(64) primary key,
  node_type   varchar(64) not null,
  created_at  timestamp not null default current_timestamp,
  modified_at timestamp,
  properties  jsonb not null,
  deleted     boolean not null default false
);

create table edge (
  id           varchar(64) primary key,
  edge_type    varchar(64) not null,
  created_at   timestamp not null default current_timestamp,
  modified_at  timestamp,
  from_node_id varchar(64) not null references node (id),
  to_node_id   varchar(64) not null references node (id),
  properties   jsonb not null,
  deleted      boolean not null default false
); 
```

Enter fullscreen mode Exit fullscreen mode

仅仅使用两个表，我就能够将任何可以想到的对象存储在`node`表中，并且能够使用`edge`表将它任意连接到任何其他对象。此外，使用 PostgreSQL 的`jsonb`表，我不再需要修改模式来添加自定义属性/字段。

为了解决第二个问题，我在我的 SQL 库周围写了一个非常薄的包装器，允许我以一种“图形化”的方式查询我的对象图:

```
 // list all actors for the home alone movie
 Traversal<Node> t = g.nodes().type("Movie").eq("title","Home Alone").to("Actor"); 
```

Enter fullscreen mode Exit fullscreen mode

对于第三个问题，我不得不在应用程序端使用一点缓存魔法。但是由于数据模型非常简单(2 个表)，所以完成起来非常简单。

我刚刚在这里描述的解决方案最终被用于我们网站和移动应用的方方面面:[https://watch.smithsonianchannel.com](https://watch.smithsonianchannel.com)