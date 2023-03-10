# 负比较的更多陷阱

> 原文：<https://dev.to/pesse/more-pitfalls-of-the-minus-comparison-gm8>

当我在 [APEX Connect](https://dev.to/pesse/apex-connect-19-great-event-great-people-great-experience-4n8h-temp-slug-3457434) 上做演示时，[埃里克·范·罗恩](https://twitter.com/evrocs_nl)解释了我的减法的另一个陷阱，即向我比较两个视图的内容——一些人已经试图向我展示了这一点，但失败了(好吧，我没能抓住要点)。

让我们从与[最后一个例子](https://dev.to/pesse/pitfalls-of-the-minus-comparison-173i)相同的情况开始，这是一个星战角色和他们出现的电影插曲的小列表:

| **ID** | **字符** | **集** |
| --- | --- | --- |
| one | 达斯·维达 | 3, 4, 5, 6 |
| Two | 卢克·天行者 | 4, 5, 6, 7, 8 |
| three | 雷伊 | 7, 8 |

现在假设我们被主管强迫加班，现在是凌晨 3 点，我们非常累，创建了一个新视图，但是错过了`group by`语句:

```
create or replace view all_movie_characters_3
  as
  select
    sw_char.id,
    sw_char.name
  from
    star_wars_characters sw_char
    inner join appearance_in_episode ep
      on sw_char.id = ep.character_fk; 
```

Enter fullscreen mode Exit fullscreen mode

当然，这不会返回预期的结果:

| 身份证明 | 性格；角色；字母 |
| --- | --- |
| one | 达斯·维达 |
| one | 达斯·维达 |
| one | 达斯·维达 |
| one | 达斯·维达 |
| Two | 卢克·天行者 |
| Two | 卢克·天行者 |
| Two | 卢克·天行者 |
| Two | 卢克·天行者 |
| three | 雷伊 |
| three | 雷伊 |

但是如果我们应用负比较，我们将得不到任何结果，这表明两种观点是相等的:

```
(
  select * from all_movie_characters
  minus
  select * from all_movie_characters_3
)
union all
(
  select * from all_movie_characters_3
  minus
  select * from all_movie_characters
);
-- No results 
```

Enter fullscreen mode Exit fullscreen mode

问题是，减号删除了所有相等行的外观。因此，只有当我们能够确定每一行都可以通过它的内容准确识别时，它才是有用的。

你可以这样说:如果一个查询的`select * from ...`等于`select distinct * from ...`，那么减比较就有效——但在我们的例子中不是这样:

```
select 'distinct' type, count(*)
from (
       select distinct *
         from all_movie_characters_3
     )
union all
select 'all', count(*) from all_movie_characters_3; 
```

Enter fullscreen mode Exit fullscreen mode

| 类型 | 计数(*) |
| --- | --- |
| 明显的 | three |
| 全部 | Ten |

Erik 建议的解决方案是将所有列的计数加到比较中:

```
with old as (
  select id, name, count(*) number_of_equals
    from all_movie_characters
    group by id, name
  ),
  new as (
    select id, name, count(*) number_of_equals
    from all_movie_characters_3
    group by id, name
  )
(
  select * from old
  minus
  select * from new
)
union all
(
  select * from new
  minus
  select * from old
); 
```

Enter fullscreen mode Exit fullscreen mode

这里的缺点是，我们不能再使用`*`符号，我总是发现这对于快速比较非常方便，尤其是在比较包含许多列的视图时。但是，如果你想确保万无一失，就无法避免投入额外的时间。

## 替代方法

Tom Kyte 有一个非常著名的方法来比较两个查询的内容:

```
select id, name, sum(old_cnt), sum(new_cnt)
from (
  select id, name, 1 old_cnt, 0 new_cnt
    from all_movie_characters source
  union all
  select id, name, 0 old_cnt, 1 new_cnt
    from all_movie_characters_3 target
)
group by id, name
having sum(old_cnt) != sum(new_cnt); 
```

Enter fullscreen mode Exit fullscreen mode

| 身份证明 | 名字 | 总和(旧计数) | 总和(新计数) |
| --- | --- | --- | --- |
| Two | 卢克·天行者 | one | four |
| three | 雷伊 | one | Two |
| one | 达斯·维达 | one | four |

除了非常优雅和高性能之外，这种方法非常酷的一点是，您可以很容易地看到哪些行是不同的，差异在哪里。

对我来说，缺点是需要大量的输入，对于没有受过训练的读者(比如我)来说，发生的事情不是那么明显。不过，这两种方法都可以变通。

Stewart Ashton 写了很多关于这个话题的文章，甚至提出了一个帮助包，使得比较两个查询结果更加方便。你应该看看他的[博客帖子列表](https://stewashton.wordpress.com/list-of-my-posts-about-comparing-and-synchronizing-tables/)。

完整的例子可以在 [LiveSQL](https://livesql.oracle.com/apex/livesql/s/ikmoy31tr15c1p3lpvcifxhcx) 和 [github](https://github.com/pesse/sith-demo-db/blob/master/100CodeExamples/16_minus_comparison_2.sql) 上找到。

## 那么，简单的减法比较还能有用吗？

我认为它可以，用于快速比较熟悉的数据。然而，知道局限性和替代方案是有好处的。一般来说，我会说:最好选择一个需要更多努力但包含较少陷阱的解决方案。但是总有例外。