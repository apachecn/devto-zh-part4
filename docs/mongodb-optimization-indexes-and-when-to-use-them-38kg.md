# MongoDB 优化——索引以及何时使用它们

> 原文：<https://dev.to/almogvc/mongodb-optimization-indexes-and-when-to-use-them-38kg>

在设计数据模型时，你应该记住一件重要的事情:我如何索引它？

索引可以使您的数据消费更快更容易。在没有索引的情况下需要 2 秒钟才能完成的查询，在有索引的情况下只需要几毫秒就可以完成。

在 MongoDB 中，索引是完全集合扫描和快速索引扫描之间的区别。没有索引，MongoDB 将不得不为它执行的每个查询执行一次*集合扫描* **。**

MongoDB 需要在集合扫描中检查集合中的每个文档，以确定它是否适合给定的查询。

例如，假设我们有一个电影收藏。
每个文档的模型将如下所示:

```
_id: ObjectId,
name: String,
description: String,
genres: String[],
writers: String[],
directors: String[],
actors: String[],
length: Number,
rating: Number, 
release: Date,
location: {
  country: String,
  city: String
},
reviews: {
  reviewer: String,
  review: String,
  date: Date
}[] 
```

我们的查询将请求评分大于 8 的所有电影。

```
db.collection.find({ rating: { $gt: 8 } }) 
```

这个查询**在评级字段**上没有索引，将导致 MongoDB 扫描我们的电影集合中的每个文档，并检查其评级是否大于 8。
这对于不需要快速返回结果的较小的集合和查询来说非常好。
然而，在更大的集合中，集合扫描可以使你的应用成为完美的加载屏幕查看器。

为了解决这个问题，我们需要使用索引，并且正确地使用它们。

# MongoDB 索引

在 MongoDB 中，索引是保存部分数据集的 B 树。
索引可以存储文档中的特定字段(单字段索引)或一组字段(复合索引)，按字段值排序。
MongoDB 允许**索引文档中任何类型的字段**。

## 指标类型

MongoDB 有几种类型的索引，每一种都代表不同类型的字段或不同类型的查询。

### 单字段索引

单个字段索引是分配给文档的一个特定字段的索引，
该索引将是这个字段的一个排序结构，它将指向我们集合中的文档。

[![MongoDB_indexes_blog-Single Field.png](img/05b6c643029fbde286667e6e29d4d955.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QWnJc6X8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1559764443673/89AbnIM7f.png)

在本例中，在评级字段`{ rating: 1 }`上创建了一个升序索引。

该索引将创建一个按评级字段排序的结构，其中该结构中的每个项目都“指向”集合中所连接的文档。

为了创建单个字段索引，我们需要执行以下命令:

```
db.<collection>.createIndex({<field>: <type>}) 
```

索引的类型(1 或-1)描述了索引中项目的顺序，其中 1 表示升序，而-1 表示降序。
在我们的例子中:

```
db.movies.createIndex({ rating: 1}) 
```

这个命令将在我们的电影收藏中的分级字段上创建一个升序索引。
当使用点符号时，我们还可以在嵌入字段(另一个字段中的一个字段)上创建单个字段索引:

```
db.movies.createIndex({location.country: -1}) 
```

该命令将在电影集合的子文档 location 中的 country 字段上创建一个降序索引。
此外，还可以在整个**嵌入式文档**上创建单个字段索引，例如:

```
db.movies.createIndex({location: 1}) 
```

该命令将在整个位置字段上创建一个索引。
为了使用这个索引，查询将需要指定**子文档的所有字段**，其顺序与它在索引和我们的集合中的文档中列出的顺序相同。

### 复合指标

复合索引是一种可以同时保存文档的多个字段(最多 32 个字段)的索引。

创建复合索引时，每个字段可以有不同的排序顺序。
第一个字段将决定文档的初始顺序，第二个字段将决定第一个字段没有唯一值的所有文档的顺序(它们的第一个字段的值出现在多个文档中)。
下一个索引字段将对下一个“排序层”进行同样的操作。

例如，如果我们在`{ length: 1, rating: -1 }`上创建一个索引，该索引将从最短的视频到最长的视频排序。如果几个视频有相同的长度，他们将按其评级排序，从高到低。

[![MongoDB_indexes_blog-Compound.png](img/9a68871f4bdbc7e0f4510451c9a2ca78.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MWb4oJi9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1559681386206/NWfEubdbw.png)

这个例子展示了如何首先根据电影的长度对索引进行排序，然后当几部电影的长度相同时，再根据它们的分级进行排序。

复合索引对于包含多个字段的查询非常有用，比如:

```
db.collection
  .find({
    length: {$gt: 60, $lt: 90},
    rating: {$gt: 8}
  })
  .sort({rating: -1}) 
```

当在 length 和 rating 字段上创建复合索引时，我们的索引将“支持”查询，因此，它将执行得更快。

> 当查询中出现的所有字段也出现在索引中时，索引将支持查询。当索引支持查询时，MongoDB 将只扫描索引而不扫描文档，因此，性能会有巨大的提升。

为了创建复合索引，您需要执行以下命令:

```
db.<collection>.createIndex({
  <field1>: <type>,
  <field2>: <type>,
  ...
}) 
```

在我们的例子中:

```
db.movies.createIndex({length: 1, rating: -1}) 
```

该索引将是长度和等级字段的复合索引，其中长度将按升序排序，等级将按降序排序。

### 多键索引

数组字段上的索引称为多键索引。多键索引可以由包含标量值(数字、字符串等)的数组字段或包含嵌套文档的数组字段(如 movies 集合中的 reviews 字段)构建。

创建多键索引的命令与创建单个字段索引的命令没有什么不同

```
db.<collection>.createIndex({ <field>: <type> }) 
```

复合多键索引是可能的，但是，只有一个字段可以是数组(在特定文档中)。

例如，不能在 writers &
directors 字段上创建复合多键索引，因为它们都是数组

```
// Will not work
db.movies.createIndex({ writers: 1, directors: -1 }) 
```

> 如果对于每个文档，这些字段中只有一个是数组，则此命令将起作用，因此 document_1 可以将 writers 作为数组，将 directors 作为字符串，同时 document_2 将 writers 作为字符串，将 directors 作为数组。
> 
> 此外，如果已经创建了索引，违反索引的新文档(2 个或更多的索引字段是数组)将无法插入到集合中。

但是，作者和评级字段的复合多键索引将会起作用。

```
db.movies.createIndex({ writers: 1, rating: -1 }) 
```

### 文本索引

文本索引支持对字符串内容的文本搜索查询，它们可以包括字符串或字符串数组类型的字段。
**每个集合只能有一个文本索引**(但是这个索引可以构建在多个字段上)。
为了在单个字段上创建文本索引，执行以下命令:

```
db.<collection>.createIndex({<field>: "text"}) 
```

对于多个字段:

```
db.<collection>.createIndex({
  <field1>: "text",
  <field2>: "text",
  ...
}) 
```

如上所述，文本索引支持$text 查询操作:

```
db.<collection>.find({
  $text: {
    $search: <string>,
    $language: <string>,
    $caseSensitive: <boolean>,
    $diacriticSensitive: <boolean>
  }
}) 
```

该查询将在文本索引中指定的所有字段中搜索$search 字段中指定的字符串。

例如，在我们的电影集合中，我们将创建以下文本索引:

```
db.movies.createIndex({
  name: "text",
  description: "text",
  genres: "text",
  writers: "text",
  directors: "text",
  actors: "text"
}) 
```

我们将执行下面的查询:

```
db.movies.find({
  $text: {
    $search: "Arnold"
  }
}) 
```

该查询将搜索我们在文本索引中指定的所有字段；名称、描述、类型、作者、导演和演员。这意味着如果名字“Arnold”出现在文档中的某个字段中，文档将“返回”给我们。

* * *

文本索引可以有权重。可以给文本索引中的每个字段赋予权重，以便在进行文本搜索时使它们优先于其他字段。
要赋予字段权重，请在选项中指定权重:

```
db.<collection>.createIndex({
  <field1>: "text",
  <field2>: "text"
},
{
  weights: {
    <field1>: <weight1>,
    <field2>: <weight2>
  }
}) 
```

在我们的例子中:

```
db.movies.createIndex({
  name: "text",
  description: "\"text\","
  genres: "text",
  writers: "text",
  directors: "text",
  actors: "text"
}, 
{
  weights: {
    name: 4,
    description: "2"
  }
}) 
```

我们的名称字段的权重为 4，描述字段的权重为 2，所有其他字段的默认值为 1。这意味着名字的影响力是描述的两倍，而描述的影响力是任何其他领域(如流派和作家)的两倍。

## 我们应该索引哪些字段？

### 支持查询字段

为了让 MongoDB 在执行查询时扫描索引而不是集合，我们应该索引“支持”该查询的字段。当索引包含查询指定的所有字段时，它将支持查询。
比如:

```
db.movies.createIndex({genres: 1, rating: -1}) 
```

将支持以下查询:

```
db.movies.find({genres: 'Horror', rating: {$gt: 6}}) 
```

我们创建的索引包含查询指定的所有字段(流派和评级)，因此，它将支持它。
复合索引还可以支持指定前缀为*的字段*的查询(仅此而已)。

> 索引前缀是复合索引字段的子集，从头到尾。

例如:

```
db.movies.createIndex({genres: 1, rating: -1, length: 1}) 
```

有以下前缀:

```
{genres: 1}
{genres: 1, rating: -1} 
```

因此，将支持以下查询:

```
db.movies.find({genres: 'Comedy', rating: 5, length: {$lte: 120}})
db.movies.find({genres: 'Sci-Fi', rating: 8})
db.movies.find({genres: 'Documentry'}) 
```

这就是为什么我们的索引也将支持下一个查询:

```
db.movies.find({genres: 'Drama'}) 
```

即使查询不包含“评级”字段，索引也会支持它，因为它的前缀包含“流派”字段，这是查询中唯一的字段。

### 支持排序字段

当:
排序中字段的顺序等于索引中字段的顺序时，索引将支持排序。

#### 用单字段索引排序

当使用索引字段作为排序规则对降序/升序单字段索引执行排序操作时，索引将始终支持排序。

例如:

```
db.movies.createIndex({ rating: 1 }) 
```

将支持以下排序:

```
db.movies.find().sort({ rating: 1 })
db.movies.find().sort({ rating: -1 }) 
```

这个索引也支持降序(即使索引是升序的),因为 MongoDB 可以颠倒它的顺序。

#### 用复合指标排序

复合索引可以支持多个字段的排序。为了支持排序，复合索引必须:

*   按照排序字段的相同顺序定义它的字段

```
db.movies.createIndex({ length: 1, rating: 1 }) 
```

会支持

```
db.movies.find().sort({ length: 1, rating: 1}) 
```

但是，它不会支持:

```
db.movies.find().sort({ rating: 1, length: 1}) 
```

*   为其字段定义与指定排序相同的顺序，或者为它们定义完全相反的顺序。

```
db.movies.createIndex({ length: 1, rating: -1 }) 
```

会支持

```
db.movies.find().sort({ length: 1, rating: -1})
db.movies.find().sort({ length: -1, rating: 1}) 
```

但是不会支持

```
db.movies.find().sort({ length: 1, rating: 1})
db.movies.find().sort({ length: -1, rating: -1}) 
```

复合索引也可以支持带前缀的排序:

**前缀**必须遵守上述规则。

```
db.movies.createIndex({ length: 1, rating: -1, location.city: 1 }) 
```

将支持以下排序:

```
db.moveis.sort({ length: 1 })
db.moveis.sort({ length: -1 })

db.moveis.sort({ length: 1, rating: -1 })
db.moveis.sort({ length: -1, rating: 1 })

db.moveis.sort({ length: 1, rating: -1, location.city: 1 })
db.moveis.sort({ length: -1, rating: 1, location.city: -1 }) 
```

* * *

> 如果排序不能从索引中获得排序顺序，它将不得不在内存中进行排序。从索引中获取排序顺序通常比在内存中进行排序(没有索引)有更好的性能。
> 此外，内存排序有其局限性，**如果不使用索引**，如果使用 32 MB 内存，排序将中止。

* * *

## 注意事项

### 索引应适合 ram

为了最大化索引的性能，它应该完全适合内存。

### 指标选择性

为了创建一个真正有用的索引，它需要尽可能地分离数据。

例如，假设我们在电影收藏中的评级是 1 到 3 之间的值，这意味着，如果我们在“评级”字段上创建索引，它只会将文档分成 3 个部分(评级:1、评级:2、评级:3)。

但是，如果我们在“发行”字段上创建一个索引，数据将分成许多小部分，每个部分将代表当天发行的所有电影。

## 动起手来

为了真正知道我们应该索引哪些字段，我们需要知道我们将执行的查询。

让我们假设我们的 movies 应用程序有 3 个主要特性需要具备:

1.  通过电影的名称、描述、类型、作者、导演和演员来搜索电影。
2.  按照电影的类型和分级得到一个分类列表。
3.  获取某个演员出演的电影列表。

对于每个查询，我都使用了 [explain](https://docs.mongodb.com/manual/reference/operator/meta/explain/) 来获取相关信息。
我对每个查询进行了 20 个阶段的测试，每个阶段代表电影收藏中不同数量的文档。
每个阶段向电影集合添加 10k 个文档，然后执行每个查询 200 次，100 次使用相关索引，100 次不使用索引。
每个阶段执行的查询总量为:2 x 3 x 100 = 600。

### 先查询

当我们使用字符串搜索电影时，我们的第一个查询将需要找到电影，可能来自搜索栏。该查询将在名称、描述、类型、作者、导演和演员字段中搜索我们的字符串。

```
db.movies.find({
  $text: {
    $search: "Human"
  }
}); 
```

没有索引，我们将无法进行$ text $搜索，因此我们的查询需要如下所示:

```
db.movies.find({
  $or: [
    { name: { $regex: 'Human', $options: "i" } },
    { description: { $regex: 'Human', $options: "i" } },
    { genres: { $regex: 'Human', $options: "i" } },
    { writers: { $regex: 'Human', $options: "i" } },
    { directors: { $regex: 'Human', $options: "i" } },
    { actors: { $regex: 'Human', $options: "i" } }
  ]
}); 
```

如果我们创建了下面的文本索引，我们将能够使用$text $search

```
db.movies.createIndex({
  name: "text",
  description: "text",
  genres: "text",
  writers: "text",
  directors: "text",
  actors: "text"
}) 
```

让我们比较一下有指数和没有指数的结果。

[![first-query-average-exec.png](img/8576971f28080747852f4e5188e59c8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IDQUscoC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1560182543373/HQAA-xhOR.png)

[![first-query-95th-percent.png](img/3f958214da2aad0e681b56f3890658f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GsihMuZA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1560182552014/7RuCliTyl.png)

上一阶段的奖金数据:

退回的文档:

*   未编入索引:9，868
*   指数:9，868

检查的文件总数:

*   未编入索引:200，000
*   指数:9，868

### 第二次查询

我们的第二个查询将如下所示:

```
db.movies.find({genres: "Fantasy"}).sort({rating: -1}); 
```

[![second-query-average-exe.png](img/4fa6c76b42b3fc8c7a393178794aa672.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GkMCpXjk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1560182572210/QJYJ43adC.png)

[![second-query-95th-percen.png](img/700c1b796a0c5e7d54f8b24eff341901.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kDEwtrY4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1560182580640/lUwtSd8p4.png)

上一阶段的奖金数据:

退回的文档:

*   未编制索引:0
*   指数:19，957

检查的文件总数:

*   未编入索引:91，605
*   指数:19，957

我们可以从这些数据中看出一些不同寻常的东西...
索引查询中返回的文档数量不等于未索引查询中返回的数量...
当执行该查询时，如果没有找到索引，则没有文档返回给我们。

这是为什么呢？发生这种情况是因为我们也在这个查询中进行排序操作，当我们没有索引时，我们的排序在内存中使用了 32mb 的限制(如上所述)。
因此，我们的非索引查询达到了该限制，查询被取消。

[![second-query-amount-of-d.png](img/b33cd328d632e3bdebc7f053aea86a20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bDSaZmsN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1560205092176/iYXC49y9D.png)

正如我们在上面的图表中看到的，当我们的集合有 100k 个文档时，我们的排序变得过于苛刻。

## 第三次查询

我们的第三个也是最后一个查询如下:

```
db.collection.find({actors: 'Jules Strosin'}); 
```

[![third-query-average-exec.png](img/0e4a5245f58ae2200c53675dedc6a6f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BqOGhLr2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1560182642552/gquxkzpeI.png)

[![third-query-95th-percent.png](img/ce5690d0df6696a7f2a65812fe7e10ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--emuDXvkm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1560182653144/zG2HBWzyv.png)

我们可以看到这个查询中的索引对性能有很大的影响，从超过一秒的执行时间到不到一毫秒。

上一阶段的奖金数据:

退回的文档:

*   未编入索引:11
*   指数:11

检查的文件总数:

*   未编入索引:200，000
*   指数:11

## 奖金

索引可能会对性能产生巨大的积极影响，但它们也会变得非常大，有些会超过 1gb。你应该经常检查索引的大小，记住内存存储的索引比磁盘存储的索引要快得多，所以要确保它们有足够的空间。

[![indexes-sizes-in-bytes-v.png](img/cd14dd736d8a523833299fa8fc1696f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G-P0MU57--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1560234504578/ZM85_58Q6.png)

## 结论

索引可以使您的查询更快更简单，并防止它们在某些情况下失败。然而，他们确实有一个尺寸，这需要被重视。
在创建索引之前，考虑要优化哪些查询以及索引应该包含哪些字段。

#### 请注意

地理空间索引和散列索引不在本文讨论范围之内，要进一步了解这些内容，您可以访问 [2dsphere 索引](https://docs.mongodb.com/manual/core/2dsphere/)和[散列索引](https://docs.mongodb.com/manual/core/index-hashed/)。

#### 资源

[MongoDB 文档索引](https://docs.mongodb.com/manual/indexes/)