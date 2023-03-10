# 推出 Easy stash-Swift 中的简易数据持久性

> 原文：<https://dev.to/onmyway133/introducing-easystash-easy-data-persistence-in-swift-2mam>

这个图书馆可以在 https://github.com/onmyway133/EasyStash 找到

EasyStash 是 Swift 中一个简单的轻量级持久性框架。通过对`NSCache`和`FileManager`的简单抽象，它将我们从保存和加载对象的繁琐工作中解放出来。目前没有聪明的异步、到期处理或缓存策略，只有保存和加载。

*   [x] Swift 5
*   [x]IOs、MAC、TV 支持
*   [x]带有显式 try catch 的同步 API
*   [x]uiimage/nsimage 持续存在
*   [x]持久化可编码对象，包括原始类型
*   [x]持久化数据
*   [x]测试覆盖率

## 用法

主要的也是唯一的类是`Storage`，它封装了内存和磁盘缓存。所有涉及磁盘的操作都容易出错，我们需要显式地处理错误。

通过`Options`，我们可以为`Codable`
定制`folder`名称、`searchPathDirectory`、`encoder`和`decoder`

```
let options = Options()
options.folder = "Users"
storage = try! Storage(options: options)

try storage.save(image, forKey: "image")
try storage.save(users, forKey: "codable") 
```

在进行磁盘操作之前，首先检查内存缓存，因此我们不会经常访问磁盘。

### 保存和加载图像

适用于 UIImage 和 NSImage。因为图像和数据加载使用相同的签名，我们需要显式指定类型

```
try storage.save(image, forKey: "image")
let loadedImage: UIImage = try storage.load(forKey: "image") 
```

### 保存和加载可编码对象

使用`JSONEncoder`和`JSONDecoder`在`Data`和
之间进行序列化和反序列化

```
let user = User(name: "A", age: 10)
let cities = [City(name: "Oslo"), City(name: "New York")]

try storage.save(users, forKey: "user")
try storage.save(cities, forKey: "cities")

let loadedUser = try storage.load(forKey: "user", as: User.self)
let loadedCities = try storage.load(forKey: "cities", as: [City].self) 
```

### 保存和加载数据

```
try storage.save(object: data, forKey: "data")
let loadedData: Data = try storage.load(forKey: "data") 
```

### 保存和加载图元

虽然像`Int, String, Bool`这样的原语符合`Codable`，但是不能用`JSONEncoder`序列化成`Data`，因为 json 需要根对象。这个框架处理这种情况，所以你可以像平常一样保存和加载

```
try storage.save(100, forKey: "an int")
try storage.save(isLoggedIn, forKey: "a boolean") 
```

### 文件夹信息

EasyStash 包含了一些有用的函数来检查其`Storage`中的文件和文件夹。

检查文件是否存在

```
try storage.exists(forKey: "has_updated_profile") 
```

移除文件

```
try storage.remove(forKey: "a flag") 
```

移除所有文件

```
try storage.removeAll() 
```

列出所有文件。每个文件都有`name`、`url`、`modificationDate`和`size`信息

```
let files = try storage.files() 
```

检查文件夹大小

```
let size = try storage.folderSize() 
```

检查文件夹是否有内容

```
try storage.isEmpty() 
```

基于谓词删除文件。当我们想要清除过期的对象或基于特定标准的对象时，这是很有用的。

```
try storage.removeAll(predicate: { $0.modificationDate < migrationDate }) 
```

## 异步

`EasyStash`被设计成同步的。如果我们想做异步，很容易使用`DispatchQueue`

```
DispatchQueue.global().async {
    do {
        try storage.save(largeImage, forKey: "large_image")
    } catch {

    }
} 
```