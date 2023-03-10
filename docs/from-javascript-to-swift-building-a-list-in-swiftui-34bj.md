# 从 JavaScript 到 Swift——在 SwiftUI 中构建列表

> 原文：<https://dev.to/ghost/from-javascript-to-swift-building-a-list-in-swiftui-34bj>

所以你想用苹果新的声明式 UI 框架建立一个列表。也许你像我一样习惯于为 web 构建，你会想，“让我们模拟一个数据结构，并迭代它来制作一个列表。”很简单，或者你是这么认为的。在 JavaScript 中，您可能会这样做:

```
// Mock data structure
const racers = [
    {
        id: 1,
        name: 'Valentino Rossi',
        team: 'Yamaha'
    },
    {
        id: 2,
        name: 'Marc Márquez',
        team: 'Repsol Honda'
    },
];

// In React
racers.map(racer => (
    <p key={racer.id}>{racer.name} rides with {racer.team}</p> ));

// In ES6
const container = document.querySelector('.container');
racers.map(racer => {
    const item = document.createElement('p');
    item.setAttribute('id', racer.id);
    item.textContent = `${racer.name} rides with ${racer.team}`;
    container.appendChild(item);
}); 
```

我想我可以用 SwiftUI 做同样的事情。定义一个字典数组，并使用类似 SwiftUI 的`ForEach`或`List`视图对它们进行迭代。在 UIKit 中，`List`大约等于`UITableView`，从我和`UITableView`的经验来看，桌子希望一切都以一种非常特别的方式设置。所以小心翼翼地接近,`List`会要求我们做一些额外的事情吗，或者我们只是扔一些数据进去，世界就会变得很好？原来，还有更多设置。这个不行:

```
import SwiftUI

struct RacerList : View {

      // Mock data structure
    let racers: [[String:String]] = [
        [
            "name": "Valentino Rossi",
            "team": "Yamaha"
        ],
        [
            "name": "Marc Márquez",
            "team": "Repsol Honda"
        ]
    ]

    var body: some View {
        List(racers) { racer in
            if let name: String = racer.name, team: String = racer.team {
                Text("\(name) rides with \(team)")
            }
        }
    }
} 
```

编译器抛出这个错误:`Unable to infer complex closure return type; add explicit type to disambiguate`，本质上归结为:“嘿，我不明白你返回的是什么类型。”但是当我们打开它们的时候，我们不是说过`name`和`team`选项是字符串吗？

事实证明，问题不在于 SwiftUI 视图代码，而在于数据结构。 **Swift 是一种强类型、面向协议的语言**(为了我自己的缘故而加粗)。传递给`List`的数据需要符合`Identifiable`协议，这样它就知道如何引用每一项。

> 我喜欢这样考虑协议:协议对于类继承就像 GraphQL 对于 REST 一样。在大多数面向对象语言的常规类继承中，如果你想扩展一个类，你可以在你的新子类中从那个类获得所有的方法、属性和其他东西。使用协议，您可以将这些包袱分成不同的“片段”,您可以告诉您的新类遵循这些“片段”。与 GraphQL 非常相似，在 graph QL 中，您确切地告诉后端您想要什么和不想要什么，协议允许您确切地定义您希望您的子类(或结构)得到什么。

我们可以这样实现`Identifiable`协议:

```
import SwiftUI

// Mock data structure
struct Racer: Identifiable {
    var id: Int
    var name: String
    var team: String
}

struct RacerList : View {
    var body: some View {
        let a = Racer(id: 1, name: "Valentino Rossi", team: "Yamaha")
        let b = Racer(id: 2, name: "Marc Márquez", team: "Repsol Honda")
        let racers = [a, b]

        return List(racers) { racer in
            Text("\(racer.name) rides with \(racer.team)")
        }
    }
} 
```

耶，成功了！🙌现在，如果我们要开始对此进行一点重构，我们可以将数据结构，或者在 iOS 世界中众所周知的*模型*，与我们所有的模型放在一个单独的目录中。然后，在我们应用程序的任何地方，我们定义一个大写的 R `Racer`，编译器知道我们在引用我们的模型，因此有关于它如何符合`Identifiable`和每个属性的类型的详细信息。

搞定了。这是关于如何在来自 JavaScript 领域的 Swift 中做事的一系列松散相关的文章中的又一次往返。感谢阅读！

更多资源:

*   [苹果 SwiftUI 列表教程](https://developer.apple.com/tutorials/swiftui/building-lists-and-navigation)
*   [协议—Swift 编程语言(Swift 5.1)](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html)
*   [可识别- SwiftUI |苹果开发者文档](https://developer.apple.com/documentation/swiftui/identifiable)
*   [Swift 中不同风格的视图模型—Sundell Swift](https://www.swiftbysundell.com/posts/different-flavors-of-view-models-in-swift)