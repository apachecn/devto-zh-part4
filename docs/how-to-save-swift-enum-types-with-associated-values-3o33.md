# 如何保存带有关联值的 Swift 枚举类型

> 原文：<https://dev.to/lukabratos/how-to-save-swift-enum-types-with-associated-values-3o33>

在这篇博文中，我们将探索如何创建带有关联值的枚举，通过实现`Decoder`和`Encoder`使它们符合`Codable`协议，并将它们保存到文件中。

## 动机

在我最近开发的一个 SDK 中，我们希望提供一种功能，在我们的客户设备失去网络连接的情况下，我们可以将所有网络操作保存到持久数据存储中。
我们希望能够按照操作排队的顺序及时从客户端检索操作，并在用户的设备重新连接到互联网时执行每一个操作——我们希望实现一个队列。因为我们一直使用带有关联值的 enum 来以类型安全的方式描述操作，所以我决定进一步探索如何实现所有的需求。

## 关联值

Swift 中的关联值允许我们将任何类型的附加数据存储到 enum 案例值中。

假设我们已经在 enum 中编码了一些与用户配置文件更新相关的操作:

```
enum UserProfileOperations {
    case updateProfilePicture(URL)
    case updateDateOfBirth(Int)
} 
```

`UserProfileOperations`枚举的常量或变量可以存储具有类型`URL`的关联值的值`updateProfilePicture`，或者具有类型`Int`的关联值的值`updateDateOfBirth`。

这个例子创建了一个名为`profilePictureOperation`的新变量，并给它赋值`UserProfileOperations.updateProfilePicture`以及相关的值`URL(string: "https://via.placeholder.com/150/1ee8a4")` :

```
let profilePictureOperation = UserProfileOperations.updateProfilePicture(URL(string: "https://via.placeholder.com/150/1ee8a4")!) 
```

类似的例子还有`dateOfBirthOperation`变量，我们将 UNIX 时间戳(类型为`Int`的关联值)赋给值`UserProfileOperations.updateDateOfBirth`:

`let dateOfBirthOperation = UserProfileOperations.updateDateOfBirth(1561825023)`

## 添加可编码一致性

由于 Swift 4.1 [不能](https://forums.swift.org/t/automatic-codable-conformance-for-enums-with-associated-values-that-themselves-conform-to-codable/11499)自动合成与`Codable`的一致性，如果我们使用带有一个或多个关联值的 enum，我们必须手动完成:

我们将从创建一个符合`Codable`协议的`UserProfileOperations` enum 的扩展开始:

```
extension UserProfileOperations: Codable {
    ...
} 
```

接下来，我们将定义编码键，它将被用作在对可编码类型的实例进行编码或解码时必须包含的属性的权威列表:

```
extension UserProfileOperations: Codable {
    enum CodingKeys: CodingKey {
        case profilePictureURL
        case dobTimestamp
    }
} 
```

如果 JSON 负载中的键与我们在`CodingKeys`中定义的键不同，我们将会收到一个错误。

因为`Codable`是`Encodable`和`Decodable`协议的类型别名，我们需要确保我们的枚举符合这两个协议。让我们从实现`Decodable` : [init(from:)](https://developer.apple.com/documentation/swift/decodable/2894081-init) :
所需的方法开始

```
extension UserProfileOperations: Codable {
    ...

    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        // Decode the value of `profilePictureURL` from the decoder.
        if let profilePictureURL = try? container.decode(URL.self, forKey: .profilePictureURL) {
            self = .updateProfilePicture(profilePictureURL)
            return
        }
        if let dob = try? container.decode(Int.self, forKey: .dobTimestamp) {
            self = .updateDateOfBirth(dob)
            return
        }
        // If the keys used in serialized data format don't match with ones specified
        // in `CodingKeys`, throw an error.
        throw CodingError.message("Error: \(dump(container))")
    }
} 
```

最后，我们需要为`Encodable`提供实现，这是方法 [encode(to:)](https://developer.apple.com/documentation/swift/encodable/2893603-encode) :

```
extension UserProfileOperations: Codable {
    ...

    func encode(to encoder: Encoder) throws {
        var container = encoder.container(keyedBy: CodingKeys.self)
        switch self {
        case .updateProfilePicture(let url):
            try container.encode(url, forKey: .profilePictureURL)
        case .updateDateOfBirth(let dob):
            try container.encode(dob, forKey: .dobTimestamp)
        }
    }
} 
```

## 编码和解码

我们可以使用 [JSONEncoder](https://developer.apple.com/documentation/foundation/jsonencoder) :
对`profilePictureOperation`进行编码

```
let encoder = JSONEncoder()
let profilePictureOperation = UserProfileOperations.updateProfilePicture(URL(string: "https://via.placeholder.com/150/1ee8a4")!)
let data = try! encoder.encode(profilePictureOperation)
let jsonString = String(data: data, encoding: .utf8)!
print(jsonString) // {"profilePictureURL":"https:\/\/via.placeholder.com\/150\/1ee8a4"} 
```

反过来，使用 [JSONDecoder](https://developer.apple.com/documentation/foundation/jsondecoder) :

```
let decoder = JSONDecoder()
let profilePictureJSONString = """
{"profilePictureURL":"https://via.placeholder.com/150/1ee8a4"}
"""
let profilePictureOperation = try! decoder.decode(UserProfileOperations.self, from: profilePictureJSONString.data(using: .utf8)!) 
```

## 将值自动保存到文件中

让我们创建几个`UserProfileOperations`枚举的实例，并将它们添加到数组:

```
let johnProfilePictureURL = UserProfileOperations.updateProfilePicture(URL(string: "https://via.placeholder.com/150/197d29")!)
let johnDateOfBirthOperation = UserProfileOperations.updateDateOfBirth(1561825023)

let kateProfilePictureURL = UserProfileOperations.updateProfilePicture(URL(string: "https://via.placeholder.com/150/c70a4d")!)
let kateDateOfBirthOperation = UserProfileOperations.updateDateOfBirth(733622400)

let timProfilePictureURL = UserProfileOperations.updateProfilePicture(URL(string: "https://via.placeholder.com/150/56acb2")!)
let timDateOfBirthOperation = UserProfileOperations.updateDateOfBirth(586915200)

let operations = [johnProfilePictureURL, johnDateOfBirthOperation, kateProfilePictureURL, kateDateOfBirthOperation, timProfilePictureURL, timDateOfBirthOperation] 
```

在下一步中，我们将创建一个 JSONEncoder，并使用它将我们的操作数组编码到数据对象中，该对象将包含编码的 JSON 数据。

```
let jsonEncoder = JSONEncoder()
let data = jsonEncoder.encode(operations) 
```

最后，我们可以将这些信息保存到文件中:

```
let url = URL(fileURLWithPath: "operations")
data.write(to: url, options: .atomic) 
```

## 从文件中读取数值

```
let operationsData = try! Data(contentsOf: url)
let jsonDecoder = JSONDecoder()
let operationsArray = try! jsonDecoder.decode([UserProfileOperations].self, from: operationsData) 
```

## 结论

在这篇博文中，我们研究了相关的值，并实现了所需的方法来符合`Codable`协议。然后，我们使用`JSONEncoder`和`JSONDecoder`对值进行编码和解码。
最后，我们将数据保存到文件中，以便在需要时可以检索和反序列化。

我希望你喜欢这篇文章。有问题吗？反馈？在推特上给我打电话！

你可以在这里下载 Swift Playgrounds 文件[。](https://lukabratos.me/download/Enums.playground.zip)