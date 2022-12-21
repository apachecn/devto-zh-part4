# 属性包装的示例

> 原文：<https://dev.to/gualtierofr/examples-of-property-wrappers-15pb>

原帖[http://www.gfrigerio.com/property-wrappers/](http://www.gfrigerio.com/property-wrappers/)

今年的 WWDC 挤满了 Swift 开发人员的新东西，其中之一是 property wrappers，以前称为 property delegate。事实上，我们在 WWDC 之前就知道属性委托，因为几个月前在 Swift.org 有一个提案，你可以在这个[链接](https://forums.swift.org/t/pitch-property-delegates/21895)上阅读它，但是我们当时不知道 SwiftUI，现在很清楚属性包装器是用来做什么的。
我在看到会议后就开始玩 SwiftUI 了，但我想先熟悉一下属性包装器，因为它们是 SwiftUI 的关键部分，我想对整个画面有一个基本的了解。实际上，你可以在 SwiftUI 中编写一个应用程序，只需要知道你可以使用@State 或@EnvironmentObject，而不需要知道名称前面的@代表什么，这篇文章是关于我用 Combine 做的项目中的几个属性包装器的例子，这里没有 SwiftUI，所以你可以通过 UIKit 使用它们来熟悉属性包装器。

### [T1】简介](#intro)

什么是属性包装？我试着这样解释:属性包装器允许你定义一组公共的功能，并用它们包装一个变量。例如，Combine 定义了@Published，您可以将它赋给一个变量，这样就可以获得该变量的值的发布者。正如你在我的例子中看到的，它们可以用来删除大量冗余的代码，通过使用泛型，你可以对任何类型的变量使用相同的属性包装，就像 Combine 允许使用@Published 一样。

### 样本项目

我用我做的项目来写关于 Combine 的东西，你可以在这里找到它在 [GitHub](https://github.com/gualtierofrigerio/CombineTest)
这个项目在我之前的两篇关于 Combine 的文章中有描述:[https://dev.to/gualtierofr/combine-first-example-3ie9](https://dev.to/gualtierofr/combine-first-example-3ie9)
[https://dev . to/gualtierofr/networking-example-with-Combine-4be 2](https://dev.to/gualtierofr/networking-example-with-combine-4be2)
所以这里我只描述我如何实现属性包装器来重构部分代码，提供相同的功能。

### 过滤一个数组

我将从一个更简单的例子开始，一个能够给数组添加过滤功能的属性包装器。
而不是打电话。直接在数组上过滤我们将使用一个属性包装器，所以实际的过滤将由定义在其中的代码来执行。
虽然包装器是通用的，但我希望数组包含符合可过滤协议的对象，所以我可以在给定的字段上执行过滤。

```
protocol Filterable {
    var filterField:String { get }
}

struct User:Codable, Filterable {

    var filterField:String {
        return username
    }
...
} 
```

这样，如果我有一组用户，我想过滤他们，我会用他们用户名字段。
现在让我们看看如何定义一个属性包装器，你可以在这里找到实现

```
@propertyWrapper
struct Filtered where T: Filterable {
    var filter:String

    var filtered:[T] {
        if filter.count > 0 {
            return value.filter({
                $0.filterField.lowercased().contains(self.filter.lowercased())
            })
        }
        return value
    }

    var value:[T]

    init(initialFilter:String) {
        self.value = [] as! [T]
        self.filter = initialFilter
    }
} 
```

通过在声明结构之前使用@propertyWrapper，我们告诉编译器，每次用@Filtered 声明变量时，它都可以被包装在结构中。必须提供值，这是存储包装变量的地方。如果需要的话，我们可以在 value 上使用 getters 和 setters，在我的例子中，我希望有一个 String 类型的过滤器，并提供一个过滤属性，返回数组的过滤元素。如你所见，我希望我的数组包含符合 Filterable 的元素，所以在 filter 闭包中我可以使用$0.filterField，我知道它是一个字符串，所以我可以小写它，看看它是否包含小写的过滤器。
让我们看看过滤后的数组作为数据源的 TableViewController，你可以在这里找到实现

```
@Filtered(initialFilter:"") var users:[User]
...

func setUsers(_ users:[User]) {
    self.users = users
    ...
}

private func applyFilter(_ filter:String) {
    $users.filter = filter
    tableView.reloadData()
} 
```

首先，我们需要声明 users 数组，并指定@Filtered，这样编译器就知道这个值将被包装在 struct Filtered 中。如你所见，我们可以用参数初始化结构，在我的例子中，我将过滤器设置为空字符串。
当我们想要应用新的过滤器时会发生什么？我们可以通过 writing _users 来访问过滤后的结构。因此，如果我们想要简单的用户数组，我们可以通过 User 引用它，如果我们需要访问属性包装器的变量和函数，我们使用$users。你会在 SwiftUI 中看到很多这样的例子，只要记住当你定义一个属性包装器时 _ 对你的变量做了什么。
现在我们有了一个用户数组，并设置了一个过滤器，如何填充 UITableView 呢？我们可以像这样访问它的过滤属性

```
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return _users.filtered.count
}
...
// make the cell
let user = $users.filtered[indexPath.row]
cell.textLabel?.text = user.username 
```

如果我们想要实现不同的过滤器呢？我们可以改变属性包装器，用不同的实现公开一个新的变量，或者只改变同一个变量，只接触一个文件。

### 获取远程实体

在我关于 Combine 的第二篇文章中，我实现了一个简单的应用程序，使用公共 REST APIs 请求 3 个实体，合并它们，并在 TableView 中显示它们。
正如你在我的[数据源](https://github.com/gualtierofrigerio/CombineTest/blob/master/CombineTest/DataSource.swift)的实现中看到的，我让类似的代码重复了 3 次

```
private func getEntity(_ entity:Entity) -> AnyPublisher {
    guard let url = getUrl(forEntity: entity) else {
        return Fail(error:DataSource.makeError(withString: "cannot get url")).eraseToAnyPublisher()
    }
    return RESTClient.getData(atURL: url)
        .catch { _ in
            Fail(error:DataSource.makeError(withString: "error converting data")).eraseToAnyPublisher()
        }
        .eraseToAnyPublisher()
}

private func getAlbums() -> AnyPublisher<[Album], Never> {
    return getEntity(.Album)
        .decode(type: [Album].self, decoder: JSONDecoder())
        .catch { error in
            Just<[Album]>([])
        }
        .eraseToAnyPublisher()
}

private func getPictures() -> AnyPublisher<[Picture], Never> {
    return getEntity(.Picture)
        .decode(type: [Picture].self, decoder: JSONDecoder())
        .catch { error in
            Just<[Picture]>([])
        }
        .eraseToAnyPublisher()
}

private func getUsers() -> AnyPublisher<[User], Never> {
    return getEntity(.User)
        .decode(type: [User].self, decoder: JSONDecoder())
        .catch { error in
            Just<[User]>([])
        }
        .eraseToAnyPublisher()
} 
```

我已经做了一个通用的 getEntity 函数来删除一些样板文件(比如每次检查 URL 是否有效)，但我不满意，我认为我可以缩小这个类的范围，用更少的代码提供相同的功能。我将为此使用一个属性包装器，让我们看看

```
@propertyWrapper
struct RemoteEntity where T:Decodable {
    let url:URL?
    let baseURLString = "https://jsonplaceholder.typicode.com"
    var defaultValue:T
    var value:T

    var publisher:AnyPublisher {
        guard let url = url else {
            return Just(self.defaultValue).eraseToAnyPublisher()
        }
        return RESTClient.getData(atURL: url)
            .decode(type: T.self, decoder: JSONDecoder())
            .catch { error in
                Just(self.defaultValue).eraseToAnyPublisher()
            }
            .eraseToAnyPublisher()
    }

    init(entity:Entity) {
        self.defaultValue = [] as! T
        self.url = URL(string:baseURLString + entity.endPoint)
        self.value = defaultValue
    }
} 
```

我希望远程实体由 JSON 来表示，所以类型是可解码的，因为我希望能够将 JSON 解码成一个结构。我所有的实体都是可编码的，所以我会没事的。这个属性包装器给了我一个特定实体的发布者，如果出错，这个发布者要么返回创建时提供的默认值，要么返回解码后的值。
让我们看看如何在我们的[修改数据源](https://github.com/gualtierofrigerio/CombineTest/blob/master/CombineTest/DataSourcePW.swift)
中使用 RemoteEntity

```
class DataSourcePW {

    @RemoteEntity(entity:.Album) var albums:[Album]
    @RemoteEntity(entity:.Picture) var pictures:[Picture]
    @RemoteEntity(entity:.User) var users:[User]

    func getUsersWithMergedData() -> AnyPublisher<[User], Never> {
        return Publishers.Zip3($pictures.publisher, $albums.publisher, $users.publisher)
            .map {
                let mergedAlbums = DataSourcePW.mergeAlbums($1, withPictures: $0)
                return DataSourcePW.mergeUsers($2, withAlbums: mergedAlbums)
            }
            .eraseToAnyPublisher()
    }
} 
```

就是这样，不需要像以前一样有 getUsers，getAlbums，getPictures。我们能够删除一些样板代码，数据源的实现变得更小，更容易阅读。在我举的两个例子中，这可能更复杂，但是这是向你展示如何从你的代码库中去除重复和样板文件的最好方法。

希望你喜欢这个介绍，快乐编码！

在 beta 4 之后进行了编辑，现在您可以通过前缀 _ 而不是＄来访问属性包装器字段