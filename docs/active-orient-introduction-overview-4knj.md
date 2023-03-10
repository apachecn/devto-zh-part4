# 主动导向-简介和概述

> 原文：<https://dev.to/topofocus/active-orient-introduction-overview-4knj>

一个(快速的)[开放源码数据库](https://orientdb.com)怎么样，不用任何模式就可以使用，可以用 sql 语句查询，并且能够执行复杂的图形操作？不要忘记:通过直观的 ruby 界面可以轻松访问。后者是`ActiveOrient`。

## 先决条件

*   安装数据库([下载开源数据库](https://orientdb.com/download-2/))并分配一个用户名
*   确保安装了 ruby 2.6(通过 snap 或者 [rvm](//https::/rvm.io) )。检查是否安装了`bundle`(否则为`gem install bundle`)
*   克隆 [ActiveOrient git 归档文件](https://github.com/topofocus/active-orient.git)并捆绑 ruby 库:

```
git clone https://github.com/topofocus/active-orient.git
cd active-orient
bundle install; bundle update 
```

Enter fullscreen mode Exit fullscreen mode

现在修改`config/connect.yaml`。
如果测试、开发和生产数据库不存在，则会创建它们。

然后切换到`/bin`并运行

```
./active-orient-console -t 
```

Enter fullscreen mode Exit fullscreen mode

## 简单开始

任何数据库类都会被识别和分配。此外，在该过程中创建的任何类都可以立即使用。任何类的行为都可以在`model`-文件中定制，这些文件包含在类的创建中。

基类`V` )ertex 和`E` )dge 总是存在。从基类中继承任何用户类是很好的做法。

```
 # create a class
    V.create_class :m   # V is the base »vertex» class. M is the vertex-class created The corresponding database-class: »m«.
 # INFO->CREATE CLASS m EXTENDS 

 # create a record
    M.create name: 'Hugo', age: 46, interests: [ 'swimming', 'biking', 'reading' ]
 # INFO->CREATE VERTEX m CONTENT {"name":"Hugo","age":46,"interests":["swimming","biking","reading"]}
    # query the database
    hugo = M.where( name: 'Hugo' ).first
    hugo.to_human
    =>"<M[177:0]: age: 46, interests: [\"swimming\", \"biking\", \"reading\"], name : Hugo>" 
    # update the dataset
    hugo.update father: M.create( name: "Volker", age: 76 )  # we create an internal link
# INFO->CREATE VERTEX m CONTENT {"name":"Volker","age":76}
# INFO->update #177:0 set father = #178:0
    hugo.to_human
    => "<M[177:0]: age : 46, father : <M[178:0]: age : 76, name : Volker>, interests : [\"swimming\", \"biking\", \"reading\"], name : Hugo>"
    hugo.father.name    
    => volker
    # change array elements
    hugo.interests << "dancing"  # --> [ 'swimming', 'biking', 'reading', 'dancing' ]
#INFO->update #177:0  set interests = interests || ['dancing'] 

    M.remove hugo 
    M.delete_class  # removes the class from OrientDB and deletes the ruby-object-definition 
```

Enter fullscreen mode Exit fullscreen mode

在这个简短的介绍中，我们创建了一个顶点类 M。然后我们创建了一个带有一些属性的文档(记录)。我们使用类似于`ActiveRecord`的语法查询数据库。
然后，我们创建了一个自我参照链接，通过它我们可以访问相关信息。

最后——使用 ruby-arrays 的语法——我们向一个嵌入式列表添加了一个条目。在模式文件、模型文件或其他文件中没有任何配置的任何东西。

ActiveOrient 迷你系列的第 1 部分到此结束。
[下一部分](https://dev.to/topofocus/activeorient-joins-and-links-j2d)涵盖了单向链接和连接，sql 查询的介绍以及它们与 Orientquery 的抽象