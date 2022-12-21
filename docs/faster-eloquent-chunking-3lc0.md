# 更快更雄辩的重庆

> 原文：<https://dev.to/ryancco/faster-eloquent-chunking-3lc0>

随着应用程序的扩展，用 Laravel concrete 处理大量数据库记录会变得越来越困难。导致内存不足异常，并降低应用程序的整体速度。这是为什么呢？

当从数据库中获取结果时，您反过来将数据拉入内存。以这段代码片段为例

```
Post::all()->each(function ($post) {
    // ...
}); 
```

Enter fullscreen mode Exit fullscreen mode

这导致以下查询，将 posts 表中的所有记录加载到内存中

```
select * from posts; 
```

Enter fullscreen mode Exit fullscreen mode

通常对于只有少量记录的表，这是完全可以接受的。然而，当你积累了成千上万的帖子时，你最终会达到网络服务器的内存资源限制。

### 分块

Laravel 中一种常见的方法是使用雄辩的(通过 [BuildsQuery](https://laravel.com/api/6.x/Illuminate/Database/Concerns/BuildsQueries.html) ) [`chunk()`](https://laravel.com/api/6.x/Illuminate/Database/Concerns/BuildsQueries.html#method_chunk) 方法，该方法获取固定数量的记录，将一个较大的集合分成更多可消耗的块。

```
Post::chunk(1000, function ($post) {
    // ...
}); 
```

Enter fullscreen mode Exit fullscreen mode

虽然这可能看起来不错，但需要注意的是，改进和缺陷都存在。

首先，想象一下下面的场景:您从数据库中获取 Post 记录来更新一个在 where 子句
中使用的属性

```
Post::where('published_at', '<', now())->chunk(1000, function ($post) {
    $post->update('published_at', now());
}); 
```

Enter fullscreen mode Exit fullscreen mode

虽然这是人为的，但它举例说明了一个非常真实的问题，即这样的查询会导致一个死循环，因为在下一次执行查询时,`published_at`属性总是小于`now()`(假设精确到秒，就像 MySQL 的`timestamp`列类型或类似类型一样)。

其次，还有查询性能及其对数据库服务器的影响。上面的代码会产生类似下面的查询

```
select * from posts order by posts.id asc limit 1000 offset 9000 
```

Enter fullscreen mode Exit fullscreen mode

由于删除的记录和额外的查询约束，MySQL 无法直接到达偏移量，因此，该查询必须有效地选择前 10，000 条记录，以便只返回最后选择的 1，000 条记录。正如您所想象的那样，这将无法很好地扩展到数千甚至数百万行。这将导致数据库服务器使用不必要的资源，降低应用程序中所有其他查询的速度。

### 分块...但是更好！

为了防止不可预见的错误并提高数据库服务器的性能，我们可以使用雄辩的 [chunkById](https://laravel.com/api/6.x/Illuminate/Database/Concerns/BuildsQueries.html#method_chunkById) 方法

```
Post::where('published_at', '<', now())->chunkById(1000, function ($post) {
    $post->update('published_at', now());
}); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码片段将产生类似下面的查询

```
select * from posts where published_at < '2019-09-11 12:00:00' and id > 9000 order by id asc limit 1000 
```

Enter fullscreen mode Exit fullscreen mode

为什么这种方法被认为“更好”？

a)它允许 MySQL 完全跳过前 9000 条(假设是连续的)记录
b)由于 where 子句中的 id 约束，我们将不再重新选择已经更新的记录

### 奖金——如何？！🤔

深入到`BuildsQueries`特征的`chunkyById`方法，我们[看到](https://github.com/laravel/framework/blob/6.x/src/Illuminate/Database/Concerns/BuildsQueries.php#L92-L107)获取的最后一条记录的 id(记住，我们是按 id 升序排序的)被存储起来，并在下一个要运行的查询中用作参数。