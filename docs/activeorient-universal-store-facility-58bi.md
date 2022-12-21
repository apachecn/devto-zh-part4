# ActiveOrient:通用商店设施

> 原文：<https://dev.to/topofocus/activeorient-universal-store-facility-58bi>

在第三部分中，我们宣称图形顶点&边缘方法的继承性和灵活性打开了突然的机会。我们马上证明一下。

## 摆脱的力量

是一个多模型数据库，能够作为一个(SQL)RDMS、一个密钥库和一个图形数据库。KeyStore-Design 要求存在一个通用密钥，即`RID`。

每个记录都有一个`RID`，每个对象都通过它的`RID`被完全识别。简单，但功能强大。

```
> Person.last.rid   => "34:0"
> "#34:0".expand.to_human 
 => "<Person[34:0] (...) >" 
```

Enter fullscreen mode Exit fullscreen mode

从数据库加载的数据项带有它们的 class-id。自动加载对象并分配相应的 ruby-class。因此，模型中定义的任何方法都是可访问的，任何属性都存在，任何连接和任何连接边都等待被发现。

## 时间网格

设想一个日期项目的网格，支持一个中心目标:为给定时间段内的任何日期、连接到网格的任何项目提供均匀的访问时间。

这个图实现了

> jahr-[月份]-Monat-[日期]-Tag-[时间] - Stunde

节点通过边的网格进行交联，网格的任何一点都很容易访问。

要使用时间网格，安装[oriented b-Time-Graph-Gem](https://github.com/topofocus/orientdb_time_graph)。它创建网格并为`String`和`Date/Time`、
定义方法`to_tg`

```
> Date.today.to_tg.datum
 => Fri, 02 Aug 2019 
 #  and 
"4.6.2015".to_tg
 => #<TG::Tag:0x00000000043c8c60 (...) 
```

Enter fullscreen mode Exit fullscreen mode

#### 储存物品

通过简单地将数据库记录`assigning`到`Tag`对象，创建了通用商店设施。

让我们回到《T2》第三部的家庭，指定`Seema's`生日。我们使用专门的边缘类`HAS_BIRTHDAY`

```
> E.create_class :has_birthday
> sema = Child.where( name: 'Seema').first
> "20.6.2010".to_tg.assign vertex: sema, via: HAS_BIRTHDAY 
```

Enter fullscreen mode Exit fullscreen mode

#### 查询

让我们考虑两个用例

*   我们只是想知道好像是生日
*   我们对 2010 年 6 月的所有生日感兴趣

##### 参见圣诞节

我们可以简单地获取子记录，并根据链接找到时间网格。

```
> sema = Child.where( name: 'Seema').first.to_human
"<Child[147:0]: in: {IS_CHILD=>1, HAS_BIRTHDAY=>1}, name : Seema>" 

> seema.nodes(:in, via: /birth/).datum  # firing a query 
 #or
> seema.in_has_birthday.out                # performs action in ruby
 => [Sun, 20 Jun 2010] 
```

Enter fullscreen mode Exit fullscreen mode

这类似于 RDMS(SQL)方法。

##### 给定范围内的任意生日

假设，你有一个巨大的数据库，充满了生日，并希望挖掘数据。在 RDMS 世界里，你可以查询球洞数据库表。数据越多，花费的时间就越长。即使你只是对与 Seema 同月生日的人感兴趣，也无法避免访问商店中所有的生日记录。

时间图提供了恒定的访问时间，与大小无关。我们只是跳到我们感兴趣的范围的一个边界，并遍历接下来的 30 天记录。

```
> start = "1.6.2010".to_tg   
> start.vector( 30 ){ :out_has_birthday }
# INFO->select out_has_birthday from  ( traverse  outE('tg_grid_of').in  from #55:5042 while $depth < 30   )  where $depth >= 0 
 => ["#52:5043"] 
> start.vector( 30 ){ :out_has_birthday }.expand.nodes(via: /birth/).to_human
=> [["<TgChild[147:0]: in: {TG_IS_CHILD=>1, TG_HAS_BIRTHDAY=>1}, name : Seema>"]] 
```

Enter fullscreen mode Exit fullscreen mode

方法向量封装了遍历查询。我们遵循 30 个时间网格链接(30 天)，正在寻找一个属性 out_has_birthday。该查询返回一组`RID`(时间网格元素)。我们手动展开它们并跟随链接`HAS_BIRTHDAY`。

如果我们对特定范围内的生日计数感兴趣:

```
> start.vector( 30, function: :count ){ :out_has_birthday } 
 => 1 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

即使使用灵活的多模型数据库，数据库设计也很重要。这个简单的模式打开了可持续编程的大门。与传统的 RDMS(SQK)数据库相比，使用时间网格，依赖于时间的数据存储在统一的环境中。查询是时间不变的。这意味着:如果一个数据库成熟、增长并进化，那么它的核心——时间网格——将会有恒定的访问时间。将任何(可序列化的)ruby 对象分配给时间网格的可能性是不言而喻的。