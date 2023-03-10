# ObjectBox Swift 1.0 发布

> 原文：<https://dev.to/objectbox/objectbox-swift-1-0-released-507g>

ObjectBox Swift 1.0 来了！自 10 个月前发布第一个公开 alpha 版本以来，我们一直在努力工作，并做出了重大改变，包括[将 Swift 放在第一位](https://objectbox.io/objectbox-swift-binding-open-sourced/)、[调整性能](https://objectbox.io/speed-increase-with-swift/)，以及迭代 API。我们希望你会喜欢这个结果。

所有这一切，都是为了给你带来你期望从数据库中得到的特性，但更重要的是，我们认为这些特性令开发者高兴，并使 ObjectBox 从其他数据库中脱颖而出。让我们迅速(有意双关)进入 ObjectBox Swift 1.0:

## 建有雨燕记

```
// objectbox: entity
class Olympian {
    var id: Id = 0
    ...
}

let store = try Store(directoryPath: dbURL.path)
let box = store.box(for: Olympian.self)
let olympians: [Olympian] = try box.all()
let goldMedalist: Olympian = try box.get(sarahHoefflinId) 
```

Enter fullscreen mode Exit fullscreen mode

ObjectBox 不仅仅是一个捆绑在 Swift 上的数据库。您的数据库实体是您设计的常规 Swift 类或结构。不需要子类化特定的类(如 CoreData 的 NSManagedObject)，也不需要编写冗长的序列化代码。😊

你需要做的只是为唯一 ID 添加一个属性，构建你的项目，ObjectBox 的代码生成器会为你写一点代码，就像 Swift 编译器为可编码对象做的那样。剩下的就是在对象上调用一个简单的方法，比如 put()，把它写出来:

```
try box.put(ruthJebet) 
```

Enter fullscreen mode Exit fullscreen mode

我们试图在整个 Swift 绑定中保持这种简单性，例如，使[使用任何 RawRepresentable enum](https://swift.objectbox.io/advanced/custom-types) 变得非常容易，而无需编写任何转换代码。

## 自动模式迁移

数据库的一个常见问题是模式迁移。ObjectBox 会处理这个问题。如果添加新的属性或类，则不需要额外的迁移步骤。旧对象将继续工作，新对象将与附加字段一起保存。类似地，添加新类会将它们添加到数据库中，而没有任何容易出错的迁移步骤。

此外，您不需要维护一个专用的模式，因为**您的类和结构首先就是模式**。

## 关系

```
class Author: Entity {
    var id: Id = 0
    var name: String
    var books: ToMany<Book> = nil

    ...
}

amandaPalmer.books.append(theArtOfAsking)
try amandaPalmer.books.applyToDb() 
```

Enter fullscreen mode Exit fullscreen mode

为了确保 ObjectBox 知道如何保存对象引用，可以使用包装类。要么是 ToOne，要么是 ToMany，而不是直接引用或数组。这让 ObjectBox **从数据库中延迟加载相关对象**，只有当您实际访问相关对象时。

Swift 1.0 版本为您带来了我们的全套关系:一对多、多对多以及它们相应的反向链接。ToMany 的行为就像任何其他 Swift 集合一样，您可以使用 append()等熟悉的方法随意添加或删除对象。

## 查询

```
let query = try box.query { Author.fname == "Jeaniene" }.build()
let results = try query.find() // All Authors matching query.
// All last names of the matching authors:
let names = try query.property(Author.lname)
                     .distinct().findStrings() 
```

Enter fullscreen mode Exit fullscreen mode

当然，ObjectBox 允许您执行查询来收集数据；完整对象或单个属性(基本 Swift 数据类型)。

但是有了 ObjectBox，您就不用再摆弄查询字符串或从游标中解包数据了。你只需用你已经习惯的函数调用和操作符编写快速表达式。

此外，您可以保持类型安全保证和编译时检查。因此，您不必花费几个小时去弄清楚为什么您的查询没有返回正确的结果，而只是发现您在查询字符串的字段名中犯了一个错别字。

```
subscription = query.subscribe() { authors, error in
    if let error = error { self.reportError(error); return }
    self.authors = authors
    self.tableView.reloadData()
} 
```

Enter fullscreen mode Exit fullscreen mode

ObjectBox 允许您操作这些对象，观察查询的变化，检索结果，删除匹配查询的对象等。源代码甚至包含一个添加了 [Combine 支持](https://developer.apple.com/documentation/combine)的文件，这样你就可以与它的管道集成，以利用苹果的最新技术。

## 开源 Swift 绑定

如果您对幕后的工作方式感到好奇，请随意查看 Swift 源代码。Swift 绑定的[源代码，以及我们基于 Sourcery 的代码生成器，可以通过](https://github.com/objectbox/objectbox-swift)[我们的 Github 账户](https://github.com/objectbox)在其他项目中获得。

## 如何入门

入门 ObjectBox 是分分钟的事情。查看我们的[设置说明](https://swift.objectbox.io/install)(基于 CocoaPods)并根据[入门指南](https://swift.objectbox.io/getting-started)直接进入代码。

## 您的反馈。接下来呢？

一如既往，[我们希望听到您的反馈](https://docs.google.com/forms/d/e/1FAIpQLSd0neiviD0Yal0Tn7921w-XWI2d0ONpLm7TfVKp7OvwW2Tu2A/viewform?usp=sf_link)！你和我们一样喜欢 ObjectBox 吗？我们把我们的心放在这个产品上，并很高兴了解您的想法:您最感兴趣的功能是什么，我们错过了什么？

我们还没有写很多关于我们非常关心的一个话题:性能。我们将在后续文章中对此进行介绍。此外，期待我们的 ObjectBox Swift 1.0 基准测试，我们将很快发布它，包括源代码。