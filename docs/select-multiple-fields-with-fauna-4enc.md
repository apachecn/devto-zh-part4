# 选择多个有动物群的田地

> 原文：<https://dev.to/scottw/select-multiple-fields-with-fauna-4enc>

我正考虑在我的下一个小项目中使用动物群，并于昨晚开始试验。我想从一个文档中返回两个字段，但是语法不正确。

我在假设 [`SelectAll`](https://docs.fauna.com/fauna/current/api/fql/functions/selectall) 允许我提取多个字段的情况下工作，但结果是这是为了从一个数组中返回多个项。

我在他们的 slack 群 <sup id="fnref1">[1](#fn1)</sup> 里发了一个消息，得知可以用不止一个 [`Select`](https://docs.fauna.com/fauna/current/api/fql/functions/select) 把一个数组传递给 Lambda。

```
Map(
  Paginate(Match(Index("all_customers"))),
  Lambda("X", [
    Select(["data", "firstName"], Get(Var("X"))),
    Select(["data", "lastName"], Get(Var("X")))
    ]
  )
) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

1.  对于这样的支持来说，懈怠是一个糟糕的选择。所有的答案都锁在他们有围墙的花园里。 [↩](#fnref1)