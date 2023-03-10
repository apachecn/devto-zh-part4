# 数据库领域驱动设计

> 原文：<https://dev.to/zakwillis/database-domain-driven-design-4bk3>

# 数据库死了吗？

我指的是关系数据库。我碰巧是一名经验丰富的 SQL Server 和 Oracle 数据库开发人员。同时，我有很强的。net 和 CSharp 技能服务器端和 web 架构。这是一种罕见的组合，虽然许多人声称他们是全栈，但他们不是。原因是，他们不尊重数据库。他们不了解关系建模，不知道如何从数据库中获得良好的性能，也不知道基于集合的数据访问的好处。

我们现在有很多方法可以完全抽象出持久层，将数据检索引入应用层。实体框架在很多方面都非常出色。它结合了 linq 的强大功能——对对象进行数据访问的函数式方法，以及对 SQL 的表达式树解析。

这对许多绝对讨厌数据库的编码人员来说是天赐良机。我知道，也见过。首先，SQL 开发人员被视为顽固的怪人，他们根本不应该参与开发。

还有 NoSQL 数据库，数据可以保存在那里，提供了很大的可伸缩性，尽管没有保证数据一致性所需的 ACID 特性。

# 大多数全栈开发都是神话

堆栈中的每一层都需要几个月到几年的时间来学习，这并不奇怪。这意味着我们看到人们专业化，尽管管理神话认为每个人在每件事上都同样熟练。

之所以更适合全栈功能，是因为它减少了层间的转换。如果您的开发人员能够构建一个体面的前端和有效的数据模型，能够决定业务逻辑应该位于何处——这比让不同的团队各司其职要好得多。

# 技术和人事思想

## 对象数组是表格？

连接一切的一个共同点是对象数组。碰巧这是真的；

*   POCO 与表中的记录相同。
*   行集是记录的集合。
*   IEnumerable 与 SQL 结果集相同。
*   表变量可转换为 IEnumerable。
*   json 数组对象完美地映射到 IEnumerable 和 table 变量。

## SQL Server 有一些非常独特的功能。

*   插入的输出。*收集新的身份值。
*   表变量可以收集插入的标识记录。
*   数据库维护事务。
*   表变量在事务之外操作，在 Oracle 中，这被称为 PRAGMA 自治。

这意味着，我们可以传入很多表变量，对数据做一些处理，然后把数据传回来。如果被选择，数据层可以变成等幂的和不可变的。

## 为什么用实体框架在 C#中做太多的数据访问是一个可怕的想法

实体框架不可思议。它让编码人员能够访问数据，如果他们理解基于集合的数据访问，精通 SQL 的工程师通过 linq 获益匪浅。因为。Net 要求编译程序集，所以任何与查询逻辑有关的问题都必须重新发布。作为一个开发过大型生产系统的开发人员，能够发布一个 SQL 补丁而不是一个新的库版本是一个巨大的优势。(从技术上讲，视图、函数是编译的，但不是以相同的方式)。

英孚是一个现实问题的另一个原因是这个原因；

```
/*pseudo code, so don't shoot me :)*/

var customer = dbset<customer>;
var order = dbset<order>;
var sales = dbset<sales>; 
```

以上很可能导致多次往返。聪明的人可能会说，我们可以在上面做一个连接，但也许我们不想做。下面是单程往返旅行。

```
 SELECT * FROM customer;
SELECT * FROM order ;
SELECT * FROM sales ; 
```

突然，我们减少了与数据库的连接数量。

## 表变量和衣冠楚楚的 POCOs

Dapper 是一个不可思议的框架，它将数据库对象映射到 C#/中。Net 对象。更好的是，它支持在一次查询执行中检索多个数据集。

为了理解这个概念，这里有一些示例代码；

```
 public class typOrder
{
public int OrderId {get;set;}
public DateTime OrderDate {get;set;}
public int CustomerID {get;set;}
public int TotalItems {get;set;}
public decimal OrderCost {get;set;}
} 
```

```
create type app.typOrder as table 
(
 OrderId int, 
 OrderDate datetime, 
 CustomerID int,
 TotalItems int,
 OrderCost decimal
) 
```

更有趣的是 IEnumerable = app . typ order；

# 什么是数据库领域驱动设计(DDDD)

我读了 DDD 的书，熟读了复杂事件处理模式和命令查询责任分离(CQRS)。让我印象深刻的是，SQL Server 是多么完美，能够让数据管理和处理的重要部分保留在数据库中。

解决团队专业化的问题

很明显，多层项目失败的主要原因要么是没有足够的跨平台技能，要么是团队之间无法相互沟通。

一个团队看到的与另一个团队看到的有本质上的不同。这就是为什么业务逻辑从数据库层移到了业务层。

模型视图控制器就是为解决这个问题而创建的模式。模型(数据层)可以独立于视图(表示层)完成它的工作。事实上，我们有更多的层次。通过创建如此多的分离，前端和后端变得模糊不清。

思考对象数组。如果每一层都从根本上基于对象数组的概念进行操作，那么每个团队与其他团队的交流就会变得更加容易。当然，代码层中可能需要一些额外的表示考虑和一些更具体的逻辑，但通常情况下，数据库中存在的内容就是前端显示的内容。

这是有意义的，因为数据建模和记录状态是一个复杂的问题，最好用 SQL 来解决。

## 那么 DDDD 又是什么？

它让所有层都有聚合根、聚合、值对象和实体。我们承认在这些特征中会有可变性，但是我们可以观察任何层并看到相同的结构。这增加了团队之间的理解。

## DDDD 的例子

我不打算在这篇文章中写所有的代码。在我的项目中，我有一套完整的系统来做到这一点，但它更多的是关于概念而不是实现。

### 数据库

(所有参数/变量都是表变量)。

```
 create procedure app.SetOrder 
@typOrder app.typOrder
,@typCustomer app.typCustomer
,@typProduct app.typProduct
,@typShipping app.typShipping

AS 

DECLARE @typOrder_OUTPUT app.typOrder
,@typCustomer_OUTPUT app.typCustomer
,@typProduct_OUTPUT app.typProduct
,@typShipping_OUTPUT app.typShipping 

INSERT INTO @typOrder_OUTPUT
SELECT ... FROM ETC

...

SELECT * FROM @typOrder_OUTPUT 
SELECT * FROM @typCustomer_OUTPUT 
SELECT * FROM @typProduct_OUTPUT 
SELECT * FROM @typShipping_OUTPUT 
```

希望你能遵循上面的建议。我们传入表变量，用这些数据做一些事情，然后返回结果。重要的是，数据库负责管理数据的状态以及数据是否被返回、提交等。我遗漏了很多代码。

人们会说，代码太多了，你可以编写元数据视图来返回大量重复的代码，以避免大量相同的工作。

最重要的是，我们可以看到如何将上述结构返回到前端 MVVM。

```
public class SetOrder_Data

(
public IEnumerable<typOrder> typOrder{get;set;}
public IEnumerable<typCustomer>typCustomer {get;set;}
public IEnumerable<typProduct>typProduct{get;set;}
public IEnumerable<typShipping>typShipping {get;set;}
}

… 

return JsonData(setOrder_Data); 
```

在 API 控制器中，我们将数据库中的同一个对象返回给客户机。此外，我们没有过度发布攻击的风险，因为我们已经决定了对象。

## 关于 DDDD 的结论

这篇文章将会有很多内容被接受，也有很多内容被遗漏。值得注意的是，我的代码位于 Dapper 之上，负责 C#中的所有数据管理。我编写了许多数据库对象来使用元数据帮助自动生成代码。

这看起来似乎是一项艰巨的工作，但它极大地简化了跨层的数据表示。