# Swift - PAT 还是仿制药？

> 原文：<https://dev.to/shawonashraf/swift-pat-or-generics-5581>

### 拍拍？

在我们开始之前，让我解释一下这里的术语。PAT 表示具有相关类型的协议。pat 可以是通用的，这意味着你可以使用任何类型，([你可以参考我之前写的这篇关于 Swift 中的协议如何被用作通用类型的文章](https://dev.to/shawonashraf/swift-the-curious-genericness-of-associated-types-3747))添加类型约束等等。让我们看一个例子——

```
protocol PlayerDescriptorProtocol {
    func describeSpeciality()
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个名为`PlayerDescriptorProtocol`的协议，它有一个名为`getSpeciality`的函数。符合此协议的类型必须在此函数的实现中提供，以描述播放器的特性。到目前为止这很好，但是我们应该问，什么类型的`Player`应该符合这个协议？是足球运动员还是网球运动员？还是板球运动员？因为这样想的话，不同类型的玩家会有不同的特长，不是吗？

所以让我们把这个弄得不那么模糊。我们将在这里添加一个关联类型。

```
protocol PlayerDescriptorProtocol {
    associatedtype PlayerType
    func describeSpeciality(of type:PlayerType)
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们检查一下符合的类型。在此之前，我们先做一个玩家类型。

```
struct CricketPlayer {
    var name: String
    var age: Int
    var speciality: String
    var infoPage: String

    init(name: String, age: Int, speciality: String, infoPage: String) {
        self.name = name
        self.age = age
        self.speciality = speciality
        self.infoPage = infoPage
    }
}

let shaks = CricketPlayer(
    name: "Shakib Al Hasan",
    age: 32,
    speciality: "All-Rounder",
    infoPage: "http://www.espncricinfo.com/bangladesh/content/player/56143.html"
) 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们为我们的协议获取一个符合类型。

```
struct CricketPlayerDescriptor: PlayerDescriptorProtocol {
    typealias PlayerType = CricketPlayer

    func describeSpeciality(of type: PlayerType) {
        print("\(type.speciality)")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
var cricketDescriptor = CricketPlayerDescriptor()
cricketDescriptor.describeSpeciality(of: shaks) 
```

Enter fullscreen mode Exit fullscreen mode

我们应该期待下面的输出-

```
All-Rounder 
```

Enter fullscreen mode Exit fullscreen mode

一切都好了，对吧？我的意思是，现在让一个足球运动员或网球运动员上场不会有什么坏处。但是等一下，亲爱的华生，我们有事情要讨论。

### 这里能出什么差错？

实际上没有什么，除非你真的关心你的应用程序的底层性能。你认为编译器是如何计算出 PAT 使用什么类型的？当然，泛型类型就是这样工作的，编译器会计算出要应用的类型，但问题是它什么时候能计算出来？

#### 进入动态调度

对于 pat，编译器必须在运行时计算出类型，这意味着它必须通过动态调度来完成。这个东西到底是什么？动态调度意味着将某些事情推迟到运行时，而不是在编译时完成。在编译器不知道立即将哪种类型应用于您的函数或对象的情况下，动态调度很有帮助，因此它会将它们留待以后确定(运行时)。

让我们用一个例子来解释一下。你在一家冰淇淋店。今天有 3 种新口味，但你不知道它们味道如何！最重要的是，你碰巧有能力把它们都买下来。所以你要做的是，把它们都买下来，然后看看味道如何。(味道不好就别怪我了)。你刚刚做了动态调度！如果你在柜台要求测试员，然后买了你喜欢的味道，这将是静态调度。

#### 静态分派

静态调度正好相反。编译器已经知道该做什么，对正在使用的类型有一个清晰的概念，所以它在编译时做所有的事情。

#### 静态调度更快吗？

相比较而言，是的。但是比动态调度好吗？你必须明白这两者是针对不同的任务的，你的编译器已经被设计成让它们非常有效地工作。如果你可以两个都用，现在你必须选择一个呢？

### 仿制药和静态调度

让我们使用泛型重写相同的代码。有点不同。

```
protocol PlayerDescriptorProtocol {
    func describeSpeciality()
} 
```

Enter fullscreen mode Exit fullscreen mode

```
struct CricketPlayer : PlayerDescriptorProtocol {
    var name: String
    var age: Int
    var speciality: String
    var infoPage: String

    init(name: String, age: Int, speciality: String, infoPage: String) {
        self.name = name
        self.age = age
        self.speciality = speciality
        self.infoPage = infoPage
    }

    func describeSpeciality() {
        print(speciality)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
let shaks = CricketPlayer(
    name: "Shakib Al Hasan",
    age: 32,
    speciality: "All-Rounder",
    infoPage: "http://www.espncricinfo.com/bangladesh/content/player/56143.html"
) 
```

Enter fullscreen mode Exit fullscreen mode

```
// generic method
func describePlayer<PlayerType: PlayerDescriptorProtocol>(of type: PlayerType) {
    type.describeSpeciality()
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们来测试一下！

```
describePlayer(of: shaks)

// output : All-Rounder 
```

Enter fullscreen mode Exit fullscreen mode

### 两人做了同样的工作....

是的，他们做到了。那么，选择哪一个呢？动态还是静态？为了做出裁决，我们必须考虑以下几点-

1.  虽然由于静态调度，泛型会更快，但与动态类型的 pat 相比，它也会占用更多内存。为什么？因为对于每种类型，编译器会将新函数压入堆栈，而对于 PAT，由于动态调度，只会创建所需的函数。
2.  如果您不希望播放器类符合协议，并且还希望为描述符类保留单独的定义，该怎么办？你最终会写出更多的代码！

在我看来，挑一个你需要的。如果你不介意牺牲一点内存来获得编译器的额外性能，那么就选择泛型，否则就选择 PATs 的便利性。这更像是在`struct`和`class`之间做出选择，因为这里没有绝对的赢家，所以选择一个做好你的工作的。