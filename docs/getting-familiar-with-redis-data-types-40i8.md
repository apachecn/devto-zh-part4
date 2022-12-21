# 熟悉 Redis 数据类型

> 原文：<https://dev.to/thefrugaldev/getting-familiar-with-redis-data-types-40i8>

Redis 是一个开源的内存数据存储。尽管有许多用例，包括充当消息代理，但 Redis 通常被用作缓存机制。我最近一直在研究如何在工作中利用这项缓存服务。考虑到这一点，我想我应该写一篇关于 Redis 中支持的一些更常见的数据类型的快速帖子，以及何时您可能会使用它们。

> *注意:以下所有示例都使用了`redis-cli`，但是您可以使用[游乐场](https://try.redis.io/)随意跟进。*

## **键**

如果您已经对缓存有所了解，您会理解大多数值都是使用一个*键*来查找所述值的。在典型的缓存系统中，这个键由一个原始字符串表示。然而，在 Redis 中，密钥是二进制安全的，这意味着您可以使用像“thefrugaldev”这样简单的东西作为您的密钥，也可以使用像 JPEG 文件内容这样复杂的东西。也就是说，[在他们的文档](https://redis.io/topics/data-types-intro#redis-keys)上建议坚持使用模式(例如:“object-type:id”)而不是真正精简或复杂的键，因为这会影响查找性能。按键也可以被分配一个[有限(到期)时间](https://redis.io/commands/expire)，如下例所示。

### **例子**

```
> set key some-value
OK

# Expire key after 5 seconds
> expire key 5
(integer) 1

# Get TTL for key
> ttl key
(integer) 5

# Get key after TTL has expired
> get key
(nil) 
```

Enter fullscreen mode Exit fullscreen mode

## **琴弦**

字符串是最基本的 Redis 数据类型，最大值为 512 MB。与 Redis 中的键类似，字符串是二进制安全的，这意味着它们可以包含任何类型的数据，包括 JPEG 图像、序列化对象等。

### **何时使用琴弦**

当您需要在少量空间中编码大量数据时，字符串非常有用。这可能包括缓存 HTML 片段或页面。因为 Redis 没有专门的整数类型，所以字符串也可以用来表示以 10 为基数的 64 位有符号整数。包括 [INCR](https://redis.io/commands/incr) (递增)或 [DECR](https://redis.io/commands/decr) (递减)在内的内置 Redis 命令可以用来调整这些整数值。

### **例子**

```
> set thefrugaldev awesome
OK

> get thefrugaldev
"awesome" 
```

Enter fullscreen mode Exit fullscreen mode

## **列举**

如果您熟悉链表数据类型，那么您已经开始理解 Redis 中的列表了。Redis 中的列表本质上是字符串值的链表，按照插入顺序排序。Redis 中的列表提供了非常大的最大长度，为 2 <sup>32</sup> - 1 个元素(超过 40 亿个元素)。类似于链表，当元素被添加到 Redis 列表中时，它们可以很快地被插入到列表的开头或结尾，因为这是一个 O(N)操作。然而，如果你正在访问一个非常大的列表的中间部分，这些操作会变得非常慢。

### **何时使用列表**

Redis 列表的一个非常常见的用例是社交网络示例。Twitter 使用 Redis 列表来实时显示最新的推文。Redis 还推荐使用列表进行进程间的通信，其中一个进程(生产者)将元素放到列表中，然后另一个进程(消费者)将项目从列表中取出并采取一些行动。

### **例子**

```
# Create a new list
# todolist: "cut the grass"
> lpush todolist "cut the grass"
(integer) 1

# Add a new item to the list
# todolist: "cut the grass", "make the bed"
> rpush todolist "make the bed"
(integer) 2

# Add an item to the beginning of the list
# todolist: "pay the electricity bill!", "cut the grass", "make the bed"
> lpush todolist "pay electricity bill!"
(integer) 3

# Remove an item from the list
# todolist: "pay the electricity bill!", "cut the grass"
> rpop todolist
"make the bed" 
```

Enter fullscreen mode Exit fullscreen mode

## **集**

Redis 集合是一个*无序的*字符串集合。因为集合返回给用户的顺序没有保证，所以操作总是 *O* (1)，或者常数时间，而不管集合中元素的数量。Redis 中的集合永远不允许重复的成员，所以多次添加相同的元素仍然会导致集合只包含元素的一个副本。这意味着添加一个元素不需要初始检查它是否已经存在于集合中。在 Redis 集合中，并集、交集、差运算都是可能的，而且执行速度相当快。

### **何时使用套**

集合对于识别实体之间的关系非常有用。例如，跟踪组成团队的个人。因为集合值总是唯一的，所以利用集合的另一个好例子是跟踪网站或网页的唯一访问者。

### **例子**

```
# Create a set for an amazing basketball team
> sadd "chicago bulls" "michael jordan" "scottie pippen" "dennis rodman" "luc longley" "ron harper"
(integer) 5

# Get all the members in the set
# note that the returned set is not in the order they were added
> smembers "chicago bulls"
1) "scottie pippen"
2) "michael jordan"
3) "luc longley"
4) "dennis rodman"
5) "ron harper"

# Determine if a given value is a member of a set
> sismember "chicago bulls" "michael jordan"
(integer) 1

> sismember "chicago bulls" "larry bird"
(integer) 0 
```

Enter fullscreen mode Exit fullscreen mode

## **哈希**

Redis 散列是字符串字段和字符串值之间的映射，非常适合存储更复杂的数据。考虑到这一点，它们非常适合表示对象，并且像列表一样，哈希可以存储超过 2<sup>32</sup>-1(40 亿)个字段-值对。

### **何时使用套**

如果你熟悉面向对象的编程，我想很多人都熟悉，那么散列是表示和缓存这些对象的一种很好的方式。我在研究时读到的一个很好的例子是一个电子商务网站的库存信息。

### **例子**

```
# Create a Hash and set multiple values at once
> hmset developer:001 "username" "thefrugaldev" "height" "5'10" "favorite beer" "bourbon"
OK

# Get the value associated with a hash field
> hget developer:001 "favorite beer"
"bourbon"

# Get all the fields and values in a hash
> hgetall developer:001
1) "username"
2) "thefrugaldev"
3) "height"
4) "5'10"
5) "favorite beer"
6) "bourbon" 
```

Enter fullscreen mode Exit fullscreen mode

### **排序集**

排序集合在本质上类似于集合，它们是不重复的字符串集合。有一个微妙的区别，即一个有序集合的每个成员都与一个分数相关联。**分数**是可以精确表示的整数范围。排序后的集合使用一个双 64 位浮点数来表示分数，这将最终用于给定集合的排序。

虽然排序集合的成员是唯一的，但与集合类似，分数可以重复。已排序集合在添加项目时开始对集合进行排序，这是一个 *O* (log( *n* ))操作。这提高了从已排序集合中检索元素时的性能，使其成为一个 *O* (1)或常数时间操作，因为它已经被排序。

假设一个元素被添加到一个具有唯一分数的排序集合中，该元素将根据该分数被适当地排序。但是，如果提供的分数与集合中的另一个分数相似，元素将按字母顺序排序。另一件要注意的事情是，排序集合是可变的，这意味着它们有一个不确定的 arity，或者一个函数接受的参数的数量。

### **何时使用排序集**

我遇到的关于使用有序集合的最好的例子之一来自 Redis 自己的[文档](https://redis.io/documentation)。在在线多人游戏中跟踪排行榜时，有许多命令可以使用，包括提交新分数时的 [ZADD](https://redis.io/commands/zadd) 、 [ZRANGE](https://redis.io/commands/zrange) 来拉动排行榜的顶部，以及 [ZRANK](https://redis.io/commands/zrank) 来查找特定用户的排名，所有这些命令的速度都令人印象深刻。

### **例子**

```
# Create a sorted set with multiple members
> zadd "college football team rankings" 1 "georgia bulldogs" 3 "alabama crimson tide" 130 "florida gators" 2 "clemson tigers" 9 "notre dame" 6 "oklahoma sooners" 7 "auburn tigers" 5 "ohio state buckeyes" 8 "wisconsin" 4 "lsu tigers"
(integer) 10

# Get the score of individual members
> zscore "college football team rankings" "georgia bulldogs"
1.0
> zscore "college football team rankings" "florida gators"
130.0

# Return a range of members in a sorted set, by index
> zrange "college football team rankings" 0 8
1) "georgia bulldogs"
2) "clemson tigers"
3) "alabama crimson tide"
4) "lsu tigers"
5) "ohio state buckeyes"
6) "oklahoma sooners"
7) "auburn tigers"
8) "wisconsin"
9) "notre dame" 
```

Enter fullscreen mode Exit fullscreen mode

这些只是我最初探索 Redis 时遇到的一些常见数据类型。如果你想进一步探索，可以在[这里](https://redis.io/commands)找到数据类型及其相关命令的完整列表。我希望您已经从中获得了一些乐趣，并喜欢学习 Redis 中一些更有用的数据类型；我知道我做到了。