# ActiveOrient:连接和链接

> 原文：<https://dev.to/topofocus/activeorient-joins-and-links-j2d>

在[简介](https://dev.to/topofocus/active-orient-introduction-overview-4knj)中，我们创建了一个自引用连接。
我们来对比一下`ActiveRecord`

```
# (ActiveRecord) in file /model/person.rb
class Person
  has_many :children, class_name: 'Person', foreign_key: 'father_id'
  belongs_to :father, :class_name => 'Person'
end
# then 
 Person.find( name: 'Hugo').first.father 
 Person.find( name: 'Hugo').first.children 
```

Enter fullscreen mode Exit fullscreen mode

我们省略了建立数据库定义、定义适当的索引和通过 rake 任务执行迁移的棘手部分，并且注意，还必须插入数据。

## 插入数据

使用 ActiveOrient，我们只需创建数据库表，插入我们的数据，然后就完成了。

```
> V.create_class :person
> hugo = Person.create name: 'Hugo', 
                       father: Person.create( name: "Reimund" ),
                       children: ["Eva", "Ulli", "Uwe", "George"].map{|c| Person.create( name: c) }
=> "<Person[150:0]: children: [#146:0, 147:0, #148:0, #149:0], father : <Person[145:0]: name: Reimund>, name: Hugo>" 

> hugo.father.name   => "Reimund"
> hugo.children.name => ["Eva", "Ulli", "Uwe", "George"] 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`children`仅由它们的 database-id 表示，这等于`ActiveRecord`使用的 foreign_key。

无模式方法揭示了一种快速的解决方案，并展示了可比较的 RDMS 数据库的基本功能。但是也有极限。无模式工作的可能性并不意味着特别设计是有利的。例如，除非定义了适当的模式，否则不可能查询链接的记录。我们仍然处于数据库世界，结构化数据的世界。

## 定义属性

一个很好的做法是，对查询的主题的任何属性建立索引。我们想在`:name`属性上创建一个`notunique`索引。`:children`只是一个`list of links`，包含`Person`对象和
`:father`是一个`link`到一个`Person`的记录。但首先，让我们删除任何东西。

```
> Person.delete all: true
> Person.create_property  :name, type: :string, index: :notunique
> Person.create_property  :children, type: :link_list, :linked_class: Person
> Person.create_property  :father, type: :link, linked_class: Person
# check
> Person.print_properties
  Detected Properties for class person
    person.children -> LINKLIST -> person
    person.father   -> LINK -> person
    person.name     -> STRING 
```

Enter fullscreen mode Exit fullscreen mode

要插入另一条记录，我们必须避免创建`Reimund`人的副本。

```
pete = Person.create name: 'Pete', 
                     father: Person.upsert( where: {name: "Reimund"} ),
                     children: ["Eva", "Mathew" ].map{|c| Person.create( name: c) } 
```

Enter fullscreen mode Exit fullscreen mode

我们搜索数据库并使用现有条目，而不是为父亲创建新记录。如果没有找到条目，则插入一条记录。这保证了`:father`总是一个有效的链接。

## 查询数据库

Orientdb 数据库类和记录是用 SQL 方言查询的。`ActiveOrient`提供了一个方法`query`来构建类似于`ActiveRecord`的查询。

```
> Person.query.where( "father is NOT NULL" ).execute.name
 #INFO->select from person where  father is NOT NULL 
 => ["Peter", "Hugo"] 
> Person.query.where( 'father.name' => 'Reimund' ).projection('name').execute
 #INFO->select name from person where father.name = 'Reimund' 
 => [{:name=>"Pete"}, {:name=>"Anton"}] 
```

Enter fullscreen mode Exit fullscreen mode

第一个查询返回两条`Person`记录。我们只显示属性`:name`。第二个查询指定一个投影并返回一个散列列表。

#### 模型助手

复杂的查询可以存储在类的`model-file`中。查询是类方法。由此可见，在`/model/person.rb`中的

```
 class Person
  def self.grandchilds of: query.where('father.name' => of)
         .projection( :children )
   end
 end 
```

Enter fullscreen mode Exit fullscreen mode

class 方法返回一个`OrientQuery`。可以通过调用`to_s`和最后的`executed`来进一步修改和检查。

```
> Person.grandchilds( of: 'Reimund' ).execute.to_human
# INFO->select children from person where father.name = 'Reimund' 
["{ children: #29:5#30:5#31:5#32:4 }", "{ children: #26:5#27:5 }"]
# or
Person.grandchilds( of: 'Reimund' ).execute{|y| y[:children]}.name
 => ["Eva", "Ulli", "Uwe", "George", "Eva", "Mathew"] 
```

Enter fullscreen mode Exit fullscreen mode

For 预处理`OrientQuery.execute`将任何返回的记录暴露给可选块。显然，我们对`:children`感兴趣。该查询返回到由`ActiveOrient`展开的`Person-Objects`的链接。因此`.name`只是处理返回记录的`name`-属性。

## 结论

数据库对连接表有基本的支持。任何熟悉 RDMS(SQL)数据库的人都应该熟悉单向链接和连接以及类似 ActiveRecord 的查询。我们可以在没有任何配置的情况下开发草图(无模式)。如果需要，可以添加约束、属性、索引和验证。嵌套投影的力量远远超过了 RDMS 世界的任何事物。

面向 21 世纪，将 ruby 的强大和美丽与 SQL 相结合。

单向链接和连接很好，也很熟悉。在下一部分中，我们将这种方法扩展到双向链接，并走进图的世界。