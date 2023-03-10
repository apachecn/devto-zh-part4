# RESTful API 设计关注点

> 原文：<https://dev.to/piczmar_0/restful-api-design-concerns-n8j>

[![](img/5cd62dc494d4b9a3c45642f2e78cbe35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8v_vxUFu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lfv7sjpprc9hne7rsd6h.jpg)

*-照片由[奥迈尔·拉纳](https://unsplash.com/@omerrana?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/car-park?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄*

* * *

最近在实际生产系统中工作时，我问了自己一个问题:

REST API 应该受到当前架构选择的约束吗？

为了说明这个问题，我虚构了一个例子。让我们想象一个用于租车的系统。这项业务有点像“汽车租赁”的 Airbnb。出租汽车的小公司可以在系统中注册他们的位置，以接触更广泛的潜在客户。该系统允许他们管理自己的汽车，但不允许他们管理任何其他租赁点的汽车。

假设我们是一家初创公司，并建立了这个系统。我们从小处着手，使用关系数据库将所有汽车存储在一个表中。在此表中，每辆车都由唯一的 ID 标识。我们想为我们的系统导出一个 RESTful API。

其中，我们需要 API 来浏览一个地点的所有汽车，并获得单辆汽车的详细信息。

列出某个地点所有汽车的 API 可能类似于:

```
GET /spots/{spotId}/cars 
```

Enter fullscreen mode Exit fullscreen mode

它将返回一个汽车列表，从中我们可以获得汽车的 id。

通过 ID 获取汽车的 API 可能类似于:

```
GET /spots/{spotId}/cars/{carId} 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
GET /cars/{carId} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们希望与 API 设计的良好实践保持一致，所以我们决定走更长的路，因为汽车是不能单独存在的资源，并且总是属于给定的地点。路径`/spots/{spotId}/cars`清楚地解释了这种关系。

但是，路径中的`spotId`是多余的。
因为我们在单个表中有所有的汽车，并且我们知道汽车 ID，因为我们从`/spots/{spotId}/cars`端点获得它，所以我们真正需要的唯一变量是`carId`。
当然，在我们的关系数据库中，我们会有从汽车到地点的关系，我们可以在我们的查询中添加`spotId`，但这并不重要。

例如，我们可以有这样一个查询:

```
select c.* from cars c inner join spots s
on s.id = c.spot_id
where s.id = :spotId and c.id = :carId 
```

Enter fullscreen mode Exit fullscreen mode

但是它会得到与:
相同的结果

```
select * from cars 
where id = :carId 
```

Enter fullscreen mode Exit fullscreen mode

那么，应该用`/spots/{spotId}/cars/{carId}`还是`/cars/{carId}`作为端点路径呢？

我一直在考虑这个问题，两种选择都有利弊。如前所述，从 API 的语义角度来看，较长的代码听起来更合适，但较短的代码在后端架构的当前状态下更容易使用和实现。

如果我们考虑我们服务的发展，那么我们可以想象我们可能想要将 cars 表拆分成每个点的单独部分。如果数据量增长，或者如果我们想要分布数据库并在每个点附近的位置设置几个实例(为了更好的性能和可伸缩性)，这可能会发生。然后，每辆车都是唯一的，但是在单个 DB 实例中(或者如果我们考虑给定地点的特定位置的一组实例，则在多个实例中)。那么我们只能通过一对`spotId`和`carId`来区分汽车，较长的 API 路径会更有意义。

最后，我对自己回答:
API 不是静止的。随着架构的发展，API 也在发展。
目前有意义且简单的(`/cars/{id}`)在未来可能不再适用。将来，如果我需要为每个租车点将汽车存储拆分到单独的表/数据库中，新的 API 可能看起来像:`/spots/{spotId}/cars/{carId}`。另一方面，这可能永远不会发生，正如 Donald Knuth 曾经说过的“过早优化是万恶之源”。

你对这个问题的答案是什么。如果你有更多的想法，请在评论中与我和其他读者分享。