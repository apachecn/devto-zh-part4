# 关于铁路的再研究

> 原文：<https://dev.to/pezza/redisearch-on-rails-581p>

如果您正在使用 Rails，那么您可能会使用 Redis 来做一些事情，无论是作为缓存、ActionCable 还是 ActiveJob。那么为什么不把它用在更多的事情上呢？

从 Redis 的第 4 版开始，Redis 模块被引入，它们是为扩展 Redis 的功能而构建的插件。第一个模块是 RediSearch，一个建立在 Redis 之上的文本搜索引擎。根据 [RediSearch.io](https://redisearch.io) :

> 与其他 Redis 搜索库不同，它不像排序集那样使用 Redis 的内部数据
> 结构。使用自己高度优化的数据结构和算法，它允许高级搜索功能、高 T2 性能和低内存占用。它可以执行简单的文本搜索，如
> 以及复杂的结构化查询，通过数字属性和
> 地理距离进行过滤。RediSearch 支持连续索引而不会降低性能，维护查询和索引的并发负载。这使得它非常适合搜索频繁更新的数据库，而不需要批量索引和服务中断。

RediSearch 的一些主要功能包括:

*   文档中多个字段的全文索引，包括:
    *   精确短语匹配。
    *   多种语言的词干。
    *   前缀查询。
    *   可选、否定和联合查询。
*   对数十亿份文档进行分布式搜索。
*   数字属性索引。
*   地理索引和半径过滤器。
*   没有性能损失的增量索引。
*   具有模糊匹配的强大自动完成引擎。
*   文档的并发低延迟插入和更新。
*   这个[基准](https://redislabs.com/blog/search-benchmarking-redisearch-vs-elasticsearch/)对抗 ElasticSearch！

让我们来看看如何将 RediSearch 集成到您的 Rails 应用程序中！

首先，从安装 Redis 和 RediSearch 开始。查看 [Redis.io](https://redis.io/download) 获取 Redis 的完整安装说明。如果可以自制，你可以`brew install redis`。从 1.6 版开始，要构建重新搜索，请执行以下操作:

1.  git 翻制[https://github . com/re search/re search . git](https://github.com/RediSearch/RediSearch.git)
2.  重新搜索 cd
3.  制造

一旦建立了 RediSearch，您需要告诉 Redis 加载该模块。最好的方法是将`loadmodule /path/to/redisearch.so`添加到 redis.conf 文件中，以便总是加载该模块。(在 macOS 上，redis.conf 文件可以在`/usr/local/etc/redis.conf`找到)。一旦 conf 文件被更新，重新启动 Redis。有关安装 RediSearch 的完整说明，请访问[rede search . io](https://oss.redislabs.com/redisearch/Quick_Start.html)。

或者，您可以使用`docker run -p 6379:6379 redislabs/redisearch:latest`运行 Redis 并使用 Docker 进行重新搜索。

一旦 Redis 和 RediSearch 安装完毕，将`redi_search` gem 添加到您的 gem 文件:

```
gem 'redi_search' 
```

Enter fullscreen mode Exit fullscreen mode

然后运行`bundle install`。或者你可以从 [GitHub 包注册表](https://github.com/npezza93/redi_search/packages/12707)安装。

安装完成后，我们需要创建一个初始化器来配置 Redis 连接。

```
# config/initializers/redi_search.rb
RediSearch.configure do |config|
  config.redis_config = {
    host: "127.0.0.1",
    port: "6379"
  }
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的应用程序中有了 RediSearch，让我们用它来索引一个模型。我们将使用一个具有`first`和`last`属性的`User`模型作为例子。

```
class User < ApplicationRecord
  redi_search schema: {
    first: { text: { phonetic: "dm:en" } },
    last: { text: { phonetic: "dm:en" } }
  }
end 
```

Enter fullscreen mode Exit fullscreen mode

在模型内部调用`redi_search`类方法接受一个必需的命名参数`schema`。这定义了索引中的字段以及这些字段的属性。重新搜索有文本、数字、地理和标签字段。上面传递了语音选项，因为我们正在索引姓名，这样可以更容易地搜索发音相似但拼写不同的姓名。不同字段类型可用选项的完整列表可在[这里](https://github.com/npezza93/redi_search#schema)找到。

```
User.reindex 
```

Enter fullscreen mode Exit fullscreen mode

在模型中调用`redi_search`类方法增加了几个有用的方法，包括做几件事的`reindex`:

1.  如果索引不存在，则创建索引
2.  调用`search_import`范围从数据库中获取所有记录
3.  将这些记录转换为重新搜索
4.  将所有这些文件编入 Redis

```
User.search("jak") 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经索引了所有的用户，我们可以开始搜索他们了。查询类似于 ActiveRecord 接口，其中子句和条件可以链接在一起，搜索是延迟执行的。一些简单的查询有:

```
# simple phrase query - jak AND daxter
User.search("jak").and("daxter")

# exact phrase query - jak FOLLOWED BY daxter
User.search("jak daxter")

# union query - jak OR daxter
User.search("jak").or("daxter")

# negation query - jak AND NOT daxter
User.search("jak").and.not("daxter") 
```

Enter fullscreen mode Exit fullscreen mode

一些更复杂的查询有:

```
# intersection of unions - (hello OR halo) AND (world OR werld)
User.search(User.search("hello").or("halo")).and(User.search("world").or("werld"))
# negation of union - hello AND NOT (world or werld)
User.search("hello").and.not(User.search("world").or("werld"))
# union inside phrase - hello AND (world OR werld)
User.search("hello").and(User.search("world").or("werld")) 
```

Enter fullscreen mode Exit fullscreen mode

所有术语都支持一些可以应用的选项。

**前缀术语**:匹配以前缀开头的所有术语。(类似于 SQL 中的`like term%`)

```
User.search("hel", prefix: true)
User.search("hello worl", prefix: true)
User.search("hel", prefix: true).and("worl", prefix: true)
User.search("hello").and.not("worl", prefix: true) 
```

Enter fullscreen mode Exit fullscreen mode

**可选条款**:包含可选条款的文档将比没有
的文档排名靠前

```
User.search("foo").and("bar", optional: true).and("baz", optional: true) 
```

Enter fullscreen mode Exit fullscreen mode

**模糊项**:基于 Levenshtein 距离进行匹配。支持的最大 Levenshtein 距离为 3。

```
User.search("zuchini", fuzziness: 1) 
```

Enter fullscreen mode Exit fullscreen mode

使用`where`子句:
也可以将搜索词的范围限定到特定的字段

```
# Simple field specific query
User.search.where(name: "john")
# Using where with options
User.search.where(first: "jon", fuzziness: 1)
# Using where with more complex query
User.search.where(first: User.search("bill").or("bob")) 
```

Enter fullscreen mode Exit fullscreen mode

搜索数字字段接受一个范围:

```
User.search.where(number: 0..100)
# Searching to infinity
User.search.where(number: 0..Float::INFINITY)
User.search.where(number: -Float::INFINITY..0) 
```

Enter fullscreen mode Exit fullscreen mode

搜索时，默认情况下会返回一个`Document`集合。在搜索查询上调用`#results`将执行搜索，然后在数据库中查找所有找到的记录，并返回一个 ActiveRecord 关系。

`redi_search`添加的另一个有用的方法是`spellcheck`,并对拼写错误的搜索词给出建议。

```
User.spellcheck("jimy")
  RediSearch (1.1ms)  FT.SPELLCHECK user_idx jimy DISTANCE 1
=> [#<RediSearch::Spellcheck::Result:0x00007f805591c670
    term: "jimy",
    suggestions: [#<struct RediSearch::Spellcheck::Suggestion score=0.0006849315068493151, suggestion="jimmy">,
      #<struct RediSearch::Spellcheck::Suggestion score=0.00019569471624266145, suggestion="jim">]>]
User.spellcheck("jimy", distance: 2).first.suggestions
  RediSearch (0.5ms)  FT.SPELLCHECK user_idx jimy DISTANCE 2
=> [#<struct RediSearch::Spellcheck::Suggestion score=0.0006849315068493151, suggestion="jimmy">,
 #<struct RediSearch::Spellcheck::Suggestion score=0.00019569471624266145, suggestion="jim">] 
```

Enter fullscreen mode Exit fullscreen mode

下次你在找搜索引擎的时候，试试 RediSearch 吧！您可以阅读更多选项，并在自述文件中查看更多示例:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[【npezz 93】](https://github.com/npezza93)/[【redi _ search】](https://github.com/npezza93/redi_search)

### 可以与 Rails 集成的 Ruby wrapper

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/55bf9dce5f0bea26b76090dda2c11f6e.png)](https://github.com/npezza93/redi_search)

# 再研究

一个简单但功能强大的 Ruby 包装器，它是 Redis 之上的一个搜索引擎。

## 装置

首先，需要安装 Redis 和 RediSearch。

你可以从[https://redis.io/download](https://redis.io/download)下载 Redis，点击查看[的安装说明。或者，在 macOS 或 Linux 上，你可以通过自制软件安装。](https://github.com/antirez/redis#installing-redis)

要安装 RediSearch，请查看 https://oss.redislabs.com/redisearch/Quick_Start.html 的一旦您建立了 RediSearch，如果您没有使用 Docker，您可以更新您的 redis.conf 文件，以便总是使用`loadmodule /path/to/redisearch.so`加载 RediSearch 模块。(在 macOS 上，redis.conf 文件可以在`/usr/local/etc/redis.conf`找到)

在 Redis 和 RediSearch 启动并运行之后，将下面一行添加到您的 gem 文件中:

```
gem 'redi_search'
```

Enter fullscreen mode Exit fullscreen mode

然后:

```
❯ bundle
```

Enter fullscreen mode Exit fullscreen mode

或者自己安装:

```
❯ gem install redi_search
```

Enter fullscreen mode Exit fullscreen mode

并要求它:

```
require 'redi_search'
```

Enter fullscreen mode Exit fullscreen mode

一旦安装了 gem 并需要它，您将需要用您的 Redis 配置来配置它。如果你在铁路上，这应该…

</article>

[View on GitHub](https://github.com/npezza93/redi_search)