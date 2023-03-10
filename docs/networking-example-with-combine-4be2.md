# 联合收割机联网示例

> 原文：<https://dev.to/gualtierofr/networking-example-with-combine-4be2>

原文在[http://www.gfrigerio.com/networking-example-with-combine/](http://www.gfrigerio.com/networking-example-with-combine/)

这是第二篇关于联合收割机的文章，在我的简单介绍后，你可以在这里找到

我使用的是同一个 GitHub 项目,但这一次我将重点放在网络上，我在关于未来& Promise、RxSwift 和 AlamoFire 的系列文章中使用了一个类似的例子。我将使用用户列表构建相同的表格视图控制器，调用可以在[链接](https://jsonplaceholder.typicode.com/)找到的公共可访问远程 API

### 样本项目

正如我提到的，我们将调用一些远程 API 来检索关于用户列表的信息。我已经在帖子上描述了关于未来和承诺的 API，你可以在[找到这个链接](http://www.gfrigerio.com/callbacks-vs-promises/)，所以这里只需要快速回顾一下。我进行了 3 次调用:第一次调用图片，第二次调用相册，第三次调用检索用户。然后我把图片和相册合并，相册和用户合并。
因为我需要 3 个调用成功才能继续，所以我将使用 Zip 将发布者合并在一起，并在 3 个调用完成时得到通知。您还会发现一些错误处理，因为这一次我们将处理可能失败的发布者，因为我们正在进行远程调用并处理我们无法控制的数据。

### 远程通话

由于该项目是关于网络，我认为最好从我们需要打电话开始。客户端很简单，只需要一个函数调用，这里可以看到代码

```
enum RESTClientError : Error {
    case error(error:String)
} 
```

让我们从错误处理开始。由于我们的通话可能会失败，最好还是处理一下。正如您将看到的，我最终消除了提供默认数据的错误，但是我认为这个逻辑必须在其他地方实现，而不是在 REST 客户机中。当出现错误时，我将创建一个 RESTClientError 类型的新错误，并将其传递给订阅者。

```
class func getData(atURL url:URL) -> AnyPublisher {
    let session = URLSession.shared
    return AnyPublisher { subscriber in
        let task = session.dataTask(with: url) { data, response, error in
            if let err = error {
                subscriber.receive(completion: .failure(RESTClientError.error(error: err.localizedDescription)))
            }
            else {
                if let data = data {
                    _ = subscriber.receive(data)
                    subscriber.receive(completion: .finished)
                }
                else {
                    let unknownError = RESTClientError.error(error: "Unknown error")
                    subscriber.receive(completion: .failure(unknownError))
                }
            }
        }
        task.resume()
    }
} 
```

如您所见，getData 返回一个可以发布一些数据的发布者，或者一个 RESTClientError 类型的错误。为了进行调用，我使用了 URLSession 的 dataTask 方法，如你所知，它可以返回一些数据或错误。
我们先来看看万一出错会怎么样。订户将只收到一个完成，这是一个具有两个值的枚举:完成和失败。我们创建了自定义的错误枚举，因此我们可以使用它来处理失败。我使用错误的本地化描述(如果有的话),或者如果我不知道错误类型，就使用字符串。
当调用返回一些东西时会发生什么？订户被调用两次，一次是数据，一次是完成，因为我们已经发送了所有的数据。请注意，我没有使用 receive(data)返回的值，这将是一个请求，指示订户期望接收多少个元素。我把我所有的数据都发一次，所以我不在乎需求。供您参考，该值可以是无限的，也可以是 Int。

你可以用未来达到同样的结果。引用苹果的实现:未来是最终产生一个价值，然后完成或者失败的出版商。听起来像是 REST 客户端的一个很好的候选，每个调用要么失败，要么给我们一些我们可以使用的数据。

```
class func getData(atURL url:URL) -> Future {
    let session = URLSession.shared
    return Future { promise in
        let task = session.dataTask(with: url) { data, response, error in
            if let err = error {
                promise(.failure(RESTClientError.error(err.localizedDescription)))
            }
            else {
                if let data = data {
                    promise(.success(data))
                }
                else {
                    let unknownError = RESTClientError.error("Unknown error")
                    promise(.failure(unknownError))
                }
            }
        }
        task.resume()
    }
} 
```

我们仍然需要处理一个枚举，但是这次我们有了。如果参数类型为 Data，则为成功；如果参数类型为 failure，则为错误。对我来说似乎更干净，如果我们有数据，不需要给用户打两次电话。DataSource 类的实现没有改变，所以我们可以为 REST 客户端使用 AnyPublisher 或 Future。其中一个区别是调用者不能控制它想要接收多少个值，因为未来要么产生一个值，要么产生一个错误，拥有这个功能没有意义。

### 数据源

现在让我们来看看负责从 REST 客户端获取 JSONs 并将其转换成我们需要的结构(图片、相册和用户)的[数据源类](https://github.com/gualtierofrigerio/CombineTest/blob/master/CombineTest/DataSource.swift)。这个类比处理 URLSessions 的类更复杂，所以我将用更多的步骤来描述它。让我们从对 rest 客户端
的一般调用开始

```
private func getEntity(_ entity:Entity) -> AnyPublisher {
    guard let url = getUrl(forEntity: entity) else {
        return Publishers.Fail(error:DataSource.makeError(withString: "cannot get url")).eraseToAnyPublisher()
    }
    return RESTClient.getData(atURL: url)
        .catch { _ in
            Publishers.Fail(error:DataSource.makeError(withString: "error converting data")).eraseToAnyPublisher()
        }
        .eraseToAnyPublisher()
} 
```

首先，我们尝试获取其中一个实体的 URL。如果我们失败了(实际上这不应该发生…)，我们需要告诉我们的订户我们失败了。这一次我采用了一种稍微不同的方法，我并不总是返回同一个发布者，而是向订阅者发送不同的接收事件，但是如果我们没有 URL，我会立即返回一个发布者，否则我会调用 rest 客户端并基于此返回我的发布者。

```
private func getAlbums() -> AnyPublisher<[Album], Never> {
    return getEntity(.Album)
        .decode(type: [Album].self, decoder: JSONDecoder())
        .catch { error in
            Just<[Album]>([])
        }
        .eraseToAnyPublisher()
} 
```

这是我们得到专辑的方法。我们尝试解码从刚刚看到的发布者那里收到的数据，如果出错(例如当我们返回默认数据时)，我们可以发布一个空数组。我们可以用它来达到这个目的。Just 是一个发布器，它发出一个值并结束，根据 Apple 自己的文档，它在用 catch 替换一个值时也很有用，正如示例中所发生的那样。因此，在通话结束时，一个相册数组或一个空数组将被发送给订户。
现在让我们看看如何将所有的值合并在一起。在我上一篇关于 Combine 的文章中，我使用了 CombineLatest，当你处理像我们监控的 UITextField 一样随时间变化的值时，它非常有用。这一次我们需要进行 3 次调用，并在所有调用都返回值时得到通知。为此，我们可以使用 Zip。CombineLatests 的不同之处在于，对于我们订阅的 N 个发布者，我们只被调用一次，而 CombineLatests 继续传递值。

```
func getUsersWithMergedData() -> AnyPublisher<[User], Never> {
    return Publishers.Zip3(getPictures(), getAlbums(), getUsers())
        .map {
            let mergedAlbums = DataSource.mergeAlbums($1, withPictures: $0)
            return DataSource.mergeUsers($2, withAlbums: mergedAlbums)
        }
        .eraseToAnyPublisher()
} 
```

注意，我使用$0、$1 和$2 来指代 getPictures、getAlbums 和 getUsers 的结果。我在这里返回的新 publisher 最终将发出 mergeUsers 的结果，即一组用户及其相册和图片。
最后我们来看看 to getUsersWithMergedData

```
@IBAction func showUsersTap(_ sender: Any) {
    _ = dataSource.getUsersWithMergedData().sink { users in
        DispatchQueue.main.async {
            let usersVC = UsersTableViewController()
            usersVC.setUsers(users)
            self.navigationController?.pushViewController(usersVC, animated: true)
        }
    }
    // get users and show UsersTableViewController
} 
```

我们使用。sink 以我们期望从出版商那里得到的价值来结束。

### 用户表视图

我知道我们有大量的用户，我们最终可以在一个 ui table 视图中显示他们。如果我们能在列表中搜索，那不是很好吗？这里并不真的需要，但是我将再次使用 Combine 来基于 UISearchViewController 对我们的表应用过滤器。你可以在这里看到代码
我在之前发表的关于 Combine 的帖子中已经谈到过@了。我使用相同的属性包装器来应用过滤器，所以每当变量值改变时，我可以将它应用到用户列表并过滤他们。

```
@Published var filter = ""

func setUsers(_ users:[User]) {
    self.users = users
    _ = $filter.sink { value in
        self.applyFilter(value)
    }
    filter = ""
}

func updateSearchResults(for searchController: UISearchController) {
    if let searchText = searchController.searchBar.text {
        filter = searchText
    }
    else {
        filter = ""
    }
} 
```

所以每次文本字段改变时，我更新变量过滤器，相同的值被用来过滤数组的元素。再次强调，Combine 在这里并不是必需的，我可以很容易地在 updateSearchResults 上调用 applyFilter，而不需要发布者。我用 Combine 演示了在这些场景中使用发布者也是很方便的。

就这些了，快乐编码:)